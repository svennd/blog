---
title: 'mount: unknown file system type LVM2_member'
author: svennd

date: 2016-12-09T11:13:00+00:00
url: /mount-unknown-filesystem-type-lvm2_member/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
So I'm planning on making a series, "mount: unknown file system type $TYPE". I already have [how to mount a ntfs partition][1], also [how to mount a nfs][2] on proxmox, now to be continued by another fun file system. I was going through old disks, so I came across one that had LVM2_member.

<pre>root@svennd:~# mount /dev/sdd2 /mnt/disk
mount: unknown filesystem type 'LVM2_member'</pre>

The fdisk -l already told me it is a LVM :

<pre>root@svennd:~# fdisk -l /dev/sdd

Disk /dev/sdd: 233.8 GiB, 251000193024 bytes, 490234752 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x0009345d

Device     Boot  Start       End   Sectors   Size Id Type
/dev/sdd1  *        63    208844    208782   102M 83 Linux
/dev/sdd2       208845 488247479 488038635 232.7G 8e Linux LVM</pre>

_ (/dev/sdi1 is /boot partition, /dev/sdi2 is where the /home data resides)  
_ 

Seems lvm2 tools also provide a way to check if it is lvm or not, using **lvmdiskscan **(/dev/sdd2 here)

<pre>root@svennd:~# lvmdiskscan
  /dev/sdb1  [       1.82 TiB]
  /dev/sdc2  [     149.04 GiB]
  /dev/sdd1  [     101.94 MiB]
  /dev/sdd2  [     232.71 GiB] LVM physical volume
  0 disks
  4 partitions
  0 LVM physical volume whole disks
  1 LVM physical volume</pre>

Fine, now let's scan what lv's (logical volumes) are to be found using **lvscan**

<pre>root@svennd:~# lvscan
 inactive '/dev/VolGroup00/LogVol00' [230.75 GiB] inherit
 inactive '/dev/VolGroup00/LogVol01' [1.94 GiB] inherit</pre>

Since this is an old disk in an enclosure, it is not activated on system boot. So we need to "activate" this lvm volume.

<pre>root@svennd:~# vgchange -ay
 2 logical volume(s) in volume group "VolGroup00" now active</pre>

and bam, ready to mount :

<pre>root@svennd:~# lvscan
  ACTIVE            '/dev/VolGroup00/LogVol00' [230.75 GiB] inherit
  ACTIVE            '/dev/VolGroup00/LogVol01' [1.94 GiB] inherit</pre>

now to mount :

<pre>mount /dev/VolGroup00/LogVol00 /mnt/disk</pre>

success !

&nbsp;

 [1]: https://www.svennd.be/mount-unknown-filesystem-type-ntfs-on-centos-7-2/
 [2]: https://www.svennd.be/mount-unknown-filesystem-nfs-proxmox/