---
layout: post
title: Download Latest video from YouTube channel
tags: [youtube]
---

Youtube has more and more ads nowadays and is becoming very annoying.
I like to listen to youtube while trying sleep. The ads sometimes has a higher volume than the vidoe I am watching. 

The suddenly change of the volume wakes me up and make me harder to get to sleep. 

<!--break-->

## Cronjob and yt-dlp come to rescue

I found a way to automatically download video from youtube channel everyday so I can listen in bed.

I use cronjob to schedule the download and installed yt-dlp to download the video.
[yt-dlp](https://github.com/yt-dlp/yt-dlp) is a fork of [youtuble-dl](https://youtube-dl.org/) and have much better perform.
The example cronjob looks like this:

```
4 1 * * * /home/asurin/local/bin/yt-dlp --playlist-end 1 https://www.youtube.com/@user-fe1if4qm8p/videos -P /usr/nfs/media/youtube/laiyueqian -S vcodec:h264 -o "%(playlist)s_%(release_date)s.%(ext)s" >> /home/asurin/log/laiyueqian.log 2>&1

--playlist-end 1 is used to download the latest video.
-P is to define the path to save the video.
-o defines the file name, for me only the name of the playlist and the date matters. 
-S vcodec:h264 is the format best suppoted by jekyll. Plex supports webm but you can not play video on ios without a payed version
```

## Host videos with Plex and Jellyfin

My home already have [Plex](https://www.plex.tv/) server and [Jellyfin](https://jellyfin.org/) server setup. 
To watch youtube with iphone, you need plex subscription. So I choose to use jellyfin and installed swiftfin. Swiftfin is much much better than the jellyfin ios app. Though sometimes it sill have bugs and you can not close the video you are playing.

There is a list of supported codec listed [here](https://jellyfin.org/docs/general/clients/codec-support/). H.264 is the most supported codec. 


