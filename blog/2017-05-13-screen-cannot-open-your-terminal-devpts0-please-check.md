---
title: 'screen : Cannot open your terminal ‘/dev/pts/0’ – please check.'
author: svennd

date: 2017-05-13T19:09:21+00:00
url: /screen-cannot-open-your-terminal-devpts0-please-check/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - screen

---
I hit upon this error :

<pre>Cannot open your terminal '/dev/pts/0' - please check.</pre>

after being logged in under root and su to a user profile;

<pre>su user</pre>

and then willing to resume a screen :

<pre># screen -r copy.pid
Cannot open your terminal '/dev/pts/3' - please check.</pre>

The issue is resolved using :

<pre>script /dev/null</pre>

I previously had similar issues, but this was using lxc-attach. See [this previous post.][1]

 [1]: https://www.svennd.be/screen-in-lxc-attach/