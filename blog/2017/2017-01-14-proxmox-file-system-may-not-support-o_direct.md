---
title: 'Proxmox : file system may not support O_DIRECT'
author: svennd

date: 2017-01-14T15:48:31+00:00
url: /proxmox-file-system-may-not-support-o_direct/
thumbnail: /img/2020/02/nightsky.jpg
categories:
  - proxmox
  - SysAdmin
tags:
  - proxmox

---
Some time ago, I tried to create a VM on a RAID6 machine, getting this weird error when the virtual disk was made :

<pre>file system may not support O_DIRECT</pre>

The solution is to put the "write cache" to write-through. In the newest interface :

![](/img/2017/01/writethrough.png) 

And the creation succeeded.

ps. this was an old issue, I never got to posting about it. Goodluck !