---
title: create ntfs partition on Centos
author: svennd

date: 2020-02-11T13:00:56+00:00
url: /create-ntfs-partition-on-centos/
thumbnail: /img/2020/04/centos.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - ntfs

---
I could already read [ntfs file systems ][1] but creating one seems to require more packages; I was surprised as I installed _ntfs-3g_ but still could no run :

<pre>[root@server ~]# mkfs -t ntfs /dev/sda1
mkfs.ntfs: No such file or directory
</pre>

The solution is a tiny other package :

<pre>yum install ntfsprogs</pre>

And magically :

<pre>mkfs -t ntfs /dev/sda1
Cluster size has been automatically set to 4096 bytes.
..</pre>

note : this will initialize the disk all with 0's which could take an insane amount of time;

Therefore you could use the :

-f flag for skiping zero'ing and

-Q flag for skiping checking on bad sectors :

<pre>mkfs -t ntfs -f -Q /dev/sda1
Cluster size has been automatically set to 4096 bytes.
Creating NTFS volume structures.
mkntfs completed successfully. Have a nice day.
</pre>

&nbsp;

 [1]: https://www.svennd.be/mount-unknown-filesystem-type-ntfs-on-centos-7-2/