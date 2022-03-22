---
title: 'single line power : create a swap file in linux'
author: svennd

date: 2017-01-26T11:21:38+00:00
url: /single-line-power-create-a-swap-file-in-linux/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
In case the machine has no swap, (VPS are typical clients but also containers) you can just create a file and assign it to be used as swap. So here is the magic line : (this creates a 512mb swapfile)

<pre>dd if=/dev/zero of=/swapfile bs=1024 count=512k && chown root:root /swapfile &&  chmod 0600 /swapfile && mkswap /swapfile -f && swapon /swapfile</pre>

&nbsp;

In steps ? OK :

(1) create an file filled with zero's of 1024*512k = 512M

<pre>dd if=/dev/zero of=/swapfile bs=1024 count=512k</pre>

(2) make user:group both root

<pre>chown root:root /swapfile</pre>

(3) only allow read/write (2+4) on the file

<pre>chmod 0600 /swapfile</pre>

(4) prepare this file for swap usage

<pre>mkswap /swapfile -f</pre>

(5) tell the kernel a new swap location is ready for use

<pre>swapon /swapfile</pre>

(6) not in the single line but you should also add this file to /etc/fstab in case it reboots

<pre>/swapfile    none    swap    sw    0   0</pre>

two remarks :

  * Don't use this on desktops to hibernate
  * SSD's won't like that kind of file, prefer not to place it on SSD's if you can chose.