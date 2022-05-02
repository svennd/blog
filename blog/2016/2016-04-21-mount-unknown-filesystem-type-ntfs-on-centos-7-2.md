---
title: 'mount: unknown filesystem type ‘ntfs’ on Centos 7.2'
author: svennd

date: 2016-04-21T11:41:41+00:00
url: /mount-unknown-filesystem-type-ntfs-on-centos-7-2/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
Mounting a Windows created external disk on a Linux system (Centos 7, now) and seeing :

<pre>mount: unknown filesystem type 'ntfs'</pre>

Its time to install some more free software ! The disk looks like :

<pre>[root@server svenn]# fdisk -l /dev/sdbg

Disk /dev/sdbg: 3000.6 GB, 3000592977920 bytes, 732566645 sectors
Units = sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk label type: dos
Disk identifier: 0x49208b59

    Device Boot      Start         End      Blocks   Id  System
/dev/sdbg1            2048   732565503  2930253824    7  HPFS/NTFS/exFAT</pre>

NTFS, the mark of a windows user ...  well on to solving it :

If you haven't install the epel-repo do that first :

<pre>yum install epel-release</pre>

Then install this package :

<pre>yum install ntfs-3g</pre>

and mount this little disk :

<pre>mount /dev/sdbg1 /media/tmp_disk</pre>

optionally you could specify the type like :

<pre>mount -t ntfs /dev/sdbg1 /media/tmp_disk</pre>

my /var/log/messages looks like this :

<pre>Apr 21 13:22:06 server kernel: fuse init (API version 7.22)
Apr 21 13:22:06 server systemd: Mounting FUSE Control File System...
Apr 21 13:22:06 server systemd: Mounted FUSE Control File System.
Apr 21 13:22:07 server ntfs-3g[21611]: Version 2016.2.22 integrated FUSE 27
Apr 21 13:22:07 server ntfs-3g[21611]: Mounted /dev/sdbg1 (Read-Write, label "Seagate Expansion Drive", NTFS 3.1)
Apr 21 13:22:07 server ntfs-3g[21611]: Cmdline options: rw
Apr 21 13:22:07 server ntfs-3g[21611]: Mount options: rw,allow_other,nonempty,relatime,fsname=/dev/sdbg1,blkdev,blksize=4096
Apr 21 13:22:07 server ntfs-3g[21611]: Ownership and permissions disabled, configuration type 1
</pre>

&nbsp;