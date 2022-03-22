---
title: 'attach.c: lxc_attach: 710 failed to get the init pid'
author: svennd

date: 2016-02-03T08:36:14+00:00
url: /attach-c-lxc_attach-710-failed-to-get-the-init-pid/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I was hit by this error way to early today ... (aka before the coffee)

<pre>root@server:~# lxc-attach -n 100
lxc-attach: attach.c: lxc_attach: 710 failed to get the init pid
</pre>

This is the Linux container (LXC) way of saying : the container is not running ... The fix is complex, but ill share it anyway.

<pre>lxc-start -n 100</pre>

Then just go ahead and

<pre>lxc-attach -n 100</pre>

Now to wait for the coffee rush.