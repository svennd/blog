---
title: Installing ZFS on Mint 17.3 live medium
author: svennd

date: 2016-08-26T09:54:05+00:00
url: /installing-zfs-on-mint-17-3-live-medium/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - zfs

---
So our [ZFS server][1] running Centos 7 has some problems, and we needed to verify that the data was oké, and for good measure sync our backup.  Getting ZFS on mint was surprisingly difficult, perhaps cause of my close to zero knowledge about Mint, or perhaps its hanging a bit in limbo right now ... Allot of guides on how to get booted using ZFS, but little on just the installation on Mint and not on Ubuntu. I tried (and failed) to use the [debian guide][2], the [-native- Ubuntu ppa][3] and even the [Ubuntu ][4]. In the end I found it using scraps all over the net... weird, since I was under the impression Mint was a beginner-user-friendly distro ... well I guess beginners can't have ZFS. Here goes :

Installing the [zfs-native][3] seems the best option :

<pre>sudo add-apt-repository ppa:zfs-native/stable
sudo apt-get update</pre>

Note : some errors around CD-ROM can be ignored. (I think)

<pre>W: Failed to fetch cdrom://Linux Mint 17.3 _Rosa_ - Release amd64 20160105/dists/trusty/non-free/binary-i386/Packages  Please use apt-cdrom to make this CD-ROM recognized by APT. apt-get update cannot be used to add new CD-ROMs</pre>

Next you need to install build tools :

<pre>apt-get install build-essential</pre>

It doesn't seem to have a dependency set on build-essentials, so do it in two steps ! Then finally install zfs ... ubuntu-zfs :/

<pre>apt-get install spl-dkms zfs-dkms ubuntu-zfs</pre>

What was installed is not really clear to me, but I could find my ZFS again, after doing :

<pre>modprobe zfs</pre>

Finding the zpool can be done using :

<pre>zpool import</pre>

and to import the set : (dataet = pool name)

<pre>zpool import dataset</pre>

happy recovery 🙂

&nbsp;

(03/jan/2017)

Note  : on Mint 18.1 you don't have to install the apt anymore, you can simply install

<pre>apt-get install zfs
or 
apt-get install zfsutils-linux</pre>

&nbsp;

 [1]: https://github.com/zfsonlinux/zfs/issues/5005
 [2]: https://github.com/zfsonlinux/zfs/wiki/Debian
 [3]: https://launchpad.net/~zfs-native/+archive/ubuntu/stable
 [4]: https://wiki.ubuntu.com/ZFS