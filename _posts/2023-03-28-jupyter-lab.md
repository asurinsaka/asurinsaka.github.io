---
layout: post
title: Jupyter Lab
tags: [jupyter, python]
---

In 2001, [Ipython](https://ipython.org/) was made as `an in interactive command-line terminal for Python`. It offers an enhanced read-eval-print loop (REPL) environment. 

Later, [Jupyter notebook](https://jupyter.org/) was created as a web-based interactive computing platform for creating and sharing computational documents. 

[Jupyter Lab](https://jupyter.org/) is the latest web-based interactive develop environment for notebooks, code and data. 

There is also [jupyter hub](https://jupyter.org/hub) which supports a group of users if that's your use case. 


In this post, I am going to talk about how to install and config jupyter lab. <!--break--> 


# Install jupyter lab

You can simply do `pip install jupyterlab`, then you will be able to start your jupyter lab with `jupyter lab` command. 

# jupyter_server_config

The configuration is the same as [jupyter notebook](https://stackoverflow.com/a/52210809/6142313). You can create a `~/.jupyter/jupyter_notebook_config.py` with the following information:

{% highlight python %}
c.ServerApp.ip = "0.0.0.0"                              # use all ipv4 interfaces
c.ServerApp.open_browser = False                        # do not open browser after server started
c.ServerApp.port = 18781                                # set port number for server
c.ServerApp.password = os.getenv("JUPYTER_PASSWORD")    # set password for the server
{% endhighlight %}

# install new kernel 

To use different python environment in jupyterlab, you need to install different kernels. 
In your python environment, you can install [ipykernel](https://github.com/ipython/ipykernel). Then run the following command to install it to jupyter lab:

{% highlight bash %}
python -m ipykernel install [--user] [--name <machine-readable-name>] [--display-name <"User Friendly Name">]
{% endhighlight %}

There is also a --profile param to set the profile to use for this kernel. The default directory for profiles is in `~/.ipython`. The default profile is in `~/.ipython/profile_default`

# startup scripts

The startup scripts are in `~/.ipython/<profile>/startup` folder. There is a README in `~/.ipython/profile_default/startup/`. It indicates all the `.py` and `.ipy` files in the startup directory will be loaded in lexicographical order.



