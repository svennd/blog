---
title: Finding disk serial from console/terminal
author: svennd

date: 2016-03-06T18:57:09+00:00
url: /finding-disk-serial-from-consoleterminal/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Quick way to find all the serial number from console :

<pre>hdparm -I /dev/sd[a-z] | grep 'Serial Nu'</pre>

Works in proxmox/debian.