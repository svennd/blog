---
title: 'Useful Linux command : screen'
author: svennd

date: 2015-07-18T07:29:55+00:00
url: /linux-screen/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - command
  - linux
  - screen

---
_Screen_ is one of those hidden Linux gems, when working on a remote or unstable connection, _screen_ is the way to go!  It creates a new shell that keeps on running even when you disconnect.

Using screen is as simple as typing <code class="EnlighterJSRAW" data-enlighter-language="null">screen</code> there are some options, those however, I commonly don't use, with one exception, the -S parameter, which gives you the option to enter a human readable name for the screen session. Definitely useful for long running jobs you are sure you are going to detach ! Another one is the -r option to reattach again (when your link was down, and you had to reconnect). In the default run-mode there is no log being made of the screen session and the _scrolling-back_ history buffer is rather limited, so when rsyncing for example it might be useful to log the output. This can be done using the -L flag, note that you can set the _scrolling-back_ history buffer (-h _int_) also, but this is limited anyway, so its better to log the screen. To end, some examples.

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># simply start a new screen session
screen

# start a screen session with a name
screen -S my_names

# detach from a screen
ctrl-a ctrl-d

# reattach on a screen
screen -r pid.screen_name
screen -r 14552.rysnc_to_server
screen -r 14809.pts-0.localhost
# screen list
screen -ls

# when a screen is attached, and you can't enter
screen -D 14552.attached_screen
# screen with logging
screen -L

# sometimes the screen "locks up" "freezes" you can try
ctrl-a q
# which is unblocking scroll</pre>

<img aria-describedby="caption-attachment-1068" loading="lazy" class="wp-image-1068 size-full" src="/img//2015/07/29732288-1.png" width="420" height="71" srcset="/img/2015/07/29732288-1.png 420w, /img/2015/07/29732288-1-300x51.png 300w, /img/2015/07/29732288-1-6x1.png 6w" sizes="(max-width: 420px) 100vw, 420px" />
