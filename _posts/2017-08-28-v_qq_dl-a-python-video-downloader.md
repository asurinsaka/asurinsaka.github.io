---
layout: post
title: v_qq_dl a small video downloader
tags: [python]
---

There is a Chinese video hosting website v.qq.com from Tencent company has many
interesting tv shows. The servers are in China so it would be slow to watch
in web browser. And I usually watch my TV with Nexus Player, which dose not have
a program to watch online video directly from v.qq.com.

Before I started to program my own downloader, I searched github for existing
downloader. There is one called [youtube-dl](https://rg3.github.io/youtube-dl/),
which while this article was written, dose not support v.qq.com.

The other one [you-get](https://github.com/soimort/you-get) supports v.qq.com.
However, it only download at 2KB/s while downloading directly.

So I started to program my own downloader and use proxy and multiple threaded
downloading to increase the speed. The downloader can also resume from broken
downloads.

# 1. HTTP partial requests

HTTP range requests allow to send only a portion of an HTTP message from a server
to a client. This is very useful for resuming downloads.

We can check whether the server sports partial by checking "Accept-Ranges" in the
header of the HTTP response. This is not implemented in my program right now.

# 2. Using Python to download the file

There is a very easy to use python HTTP package called [requests](http://docs.python-requests.org/en/master/) can be used to
download files from internet. A sample download code is here:
{% highlight python %}
def download_file(url):
    local_filename = url.split('/')[-1]
    # NOTE the stream=True parameter
    r = requests.get(url, stream=True)
    with open(local_filename, 'wb') as f:
        for chunk in r.iter_content(chunk_size=1024):
            if chunk: # filter out keep-alive new chunks
                f.write(chunk)
                f.flush()
    return local_filename
{% endhighlight  %}

This part is the core code for downloading. However, we need to wrap the code
with multithreading and also need to monitor the progress to support resuming.

# 3. Python multithreading

We can use either multithreading or multiprocessing to increase the download
speed. To monitor the download progress, data has to be shared between instance.
Which would be easier to be implemented in multithreading program. Recall that
different thread are using the same data.

In this program, I am using [pathos](https://pypi.python.org/pypi/pathos) package
for the multithreading for 2 reasons.
1. It can use Pool for threads to limit the number of threads
2. It use dill, which can serialize file pointer to be passed in arguments
{% highlight python %}
thread_count = 100
args = []
fps = []

# Each thread is responsible for one block of the one media file, this for loop pack
# the arguments in the args list
for i, (url, filename, info_file, size) in enumerate(zip(urls, files, info_files, sizes)):
    if os.path.isfile(filename):
        continue

    workname = filename + '.download'

    # each block has 3 parts, the start of the block, the offset, the end of the block
    if os.path.isfile(info_file) and os.path.getsize(info_file) > 0:
        _x, blocks = read_data(info_file)
    else:
        block_count, remain = divmod(size, block_size)

        blocks = [[i * block_size, i * block_size, (i+1) * block_size - 1] for i in range(block_count)]
        blocks[-1][-1] += remain

        with open(workname, 'wb') as fp:
            fp.write(b'')

    file_info = FileInfo(url, filename, size)

    threading.Thread(target=_monitor, args=(file_info, blocks, info_file)).start()

    fp = open(workname, 'rb+')
    fps.append(fp)
    new_args = [(url, blocks[i], fp, buffer_size) for i in range(len(blocks)) if blocks[i][1] < blocks[i][2]]
    if new_args is not None:
        args.extend(new_args)

if thread_count > len(args):
    thread_count = len(args)

if thread_count > 0:
    # Use a progress bar to show the downloading the progress
    threading.Thread(target=_progress_bar, args=(files, sizes)).start()
    pool = ThreadPool(thread_count)
    pool.map(_worker, args)
    pool.close()
    pool.join()
{% endhighlight  %}


# 4. Tracking progress

To be able to resume broken downloads, the program has to have the ability to
track and record downloading progress for each file. In this program, we divided
the media file into blocks and use an info file to track each block. To resuming the download, exam the info file and only download the unfinished blocks by specify range header for the HTTP requests. The core download code and the monitor code is:
{% highlight python %}
def _worker(args):
    url, block, fp, buffer_size = args
    headers = {'Range': 'bytes=%s-%s' % (block[1], block[2])}
    r = requests.get(url, stream=True, headers=headers)
    # TODO :requests.exceptions.ChunkedEncodingError: ("Connection broken: ConnectionResetError(54, 'Connection reset by peer')", ConnectionResetError(54, 'Connection reset by peer'))
    for chunk in r.iter_content(buffer_size):
        with lock:
            fp.seek(block[1])
            fp.write(chunk)
            block[1] += len(chunk)
        logging.debug('\_worker: {} {}'.format(block, headers))


def _monitor(file_info, blocks, info_file):
    while True:
        with lock:
            percent = sum([block[1] - block[0] for block in blocks]) * 100 / file_info.size
            if percent >= 100:
                break
            write_data(info_file, (file_info, blocks))
        time.sleep(2)
        logging.debug('_monitor: {} {} {}percent {}'.format(file_info.filename, file_info.size, percent, blocks))

{% endhighlight  %}


The source code of the project is [here](https://github.com/asurinsaka/v_qq_dl):
