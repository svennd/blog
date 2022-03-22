---
title: Getting hard disk serials from 3ware raid controllers
author: svennd

date: 2016-03-05T18:06:44+00:00
url: /getting-hard-disk-serials-from-3ware-raid-controllers/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I love my 3ware raid controllers, the application I use is [tw_cli][1]. but getting the serials is a bit a hidden given; you should explicitly ask the raid controller for the serial. This can be done with :

<pre>tw_cli /c0/p1 show serial</pre>

Since we are lazy, we only type it once and profit !

<pre>for i in {8..15}; do tw_cli /c0/p$i show serial; done</pre>

&nbsp;

 [1]: http://www.cyberciti.biz/files/tw_cli.8.html