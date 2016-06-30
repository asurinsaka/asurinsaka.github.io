---
layout: post
title: More History for Linux Bash
category: Linux
tags: [bash]
---

If you open Multiple sections of Linux terminal, only one copy of history will
be saved in your .bash_history file. This would be really annoying because you
will forget and loose important command that you have used.

Here is how to save all your histories.

Put the following commands in your ~/.bashrc file or your ~/.bash_profile file.

{% highlight sh %}
# append instand of overwrite across sections
shopt -s histappend

# write and read history file every time you hit return
PROMPT_COMMAND='history -n;history -a'

# increase your history entries and file size
HISTSIZE=100000
HISTFILESIZE=100000

# add date and time information for each entry
HISTTIMEFORMAT="%m/%d/%y %T "

{% endhighlight %}


Now you will have the history recorded from all the sessions you have opened.
