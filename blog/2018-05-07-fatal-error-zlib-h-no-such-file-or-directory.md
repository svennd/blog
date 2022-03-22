---
title: 'fatal error: zlib.h: No such file or directory'
author: svennd

date: 2018-05-07T08:15:28+00:00
url: /fatal-error-zlib-h-no-such-file-or-directory/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos

---
While compiling [minimap 2][1] on Centos 7, I got his annoying error :

<pre>fatal error: zlib.h: No such file or directory</pre>

Missing dependency ! I wish some tool would help out and tell you, what library I was missing, so I could stop making these posts.

As to the solution :

<pre>yum install zlib-devel</pre>

for debian :

<pre>apt-get install libz-dev</pre>

&nbsp;

&nbsp;

 [1]: https://github.com/lh3/minimap2