---
title: centos 7 from usb with YUMI
author: svennd

date: 2015-10-23T12:59:22+00:00
url: /centos-7-from-usb-with-yumi/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
This happened while installing Centos 7 from usb, with YUMI 2.0.1.8 : (just after _mounted configuration file system_)

<pre>dracut-initqueue[685]: Warning: /dev/root does not exist</pre>

It will stop there, and you are pretty much at a dead end; The error happens, cause there are multiple ways to install it in the iso and they break each other ... [weird stuff](https://www.centos.org/forums/viewtopic.php?f=47&t=47905#p203678" target="_blank).

Anyways, while [this guy seemed][1] to have found a work around, I remade the stick using good'ol fashion _data destruct-or_ better known as [dd][2].

<pre>dd bs=4M if=CentOS-7-x86_64-Minimal-1503-01.iso of=/dev/sdd</pre>

_note : sdd is my USB stick, don't try it with sda, or do and see the world burn._

Now my server runs as a baby. (it's also as slow as a baby)

 [1]: http://unix.stackexchange.com/questions/141515/centos-7-installation-failed/209515#209515
 [2]: http://linux.die.net/man/1/dd