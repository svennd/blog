---
title: error libwrap missing
author: svennd

date: 2017-03-22T15:02:10+00:00
url: /error-libwrap-missing/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
While compiling bareos I got this error :

<pre>checking for socket... yes
using socket from libc
checking for library containing nanosleep... no
checking for libwrap... configure: error: *** libwrap missing</pre>

The missing library on Centos is :

<pre>tcp_wrappers-devel</pre>

Fix :

<pre>yum install tcp_wrappers-devel</pre>