---
title: 'mount: unknown filesystem type ‘zfs_member’'
author: svennd

date: 2016-12-14T13:46:11+00:00
url: /mount-unknown-filesystem-type-zfs_member/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - filesystem
  - zfs

---
After mounting [a NTFS partiton in read/write][1], a [NFS partition ][2]and just last week a [LVM2_member partition][3]. Its time for a new episode in the series: How to mount an unknown file-type in Linux. As they say, the saga continues.

Note that I use proxmox (Debian spin) on this machines, this makes that ZFS was already installed. Installing ZFS is easy, and I done it before on [Mint][4], the [official ZFS (on linux) information][5] is also a huge help, so head over there if you wan't up-to-date install & configuration help.

[<img loading="lazy" class="alignnone wp-image-1245 " src="/img/2016/12/zfs-linux-300x187.png" width="229" height="143" srcset="/img/2016/12/zfs-linux-300x187.png 300w, /img/2016/12/zfs-linux-2x1.png 2w, /img/2016/12/zfs-linux.png 400w" sizes="(max-width: 229px) 100vw, 229px" />][6]

Every good story always starts when someone or something f*cks up. So here goes, right in the nuts :

<pre>root@svennd:~# mount /dev/sdd3 /mnt/datadisk/
mount: unknown filesystem type 'zfs_member'</pre>

That's fine, its time for our hero to move in and gain information about our target: **fdisk -l**

<pre>root@svennd:~# fdisk -l /dev/sdg

Disk /dev/sdd: 74.5 GiB, 80026361856 bytes, 156301488 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: D4DA51A9-6084-4E52-ACE7-AD0272064251

Device      Start       End   Sectors  Size Type
/dev/sdd1    2048      4095      2048    1M BIOS boot
/dev/sdd2    4096    266239    262144  128M EFI System
/dev/sdd3  266240 156299439 156033200 74.4G Linux filesystem</pre>

The "BIOS boot" and "EFI System" partition are irrelevant to me collecting data, but "Linux filesystem" is where the data is. Sadly this does not give us allot to go on. I was hoping this was part of a ZFS mirror. If it where a RAIDZ[1-3] a single disk is not going to cough up its data to easy.  ZFS comes with allot of handy tools, one is actually scanning storage devices for ZFS partitions that aren't yet active on the device and contrary to other RAID systems, ZFS is always in a state where it can be transferred between machines. So I tried to run **zpool import** :

<pre>root@svennd:~# zpool import
 pool: rpool
    id: 1395200144405345736
 state: DEGRADED
status: One or more devices contains corrupted data.
action: The pool can be imported despite missing or damaged devices.  The
       fault tolerance of the pool may be compromised if imported.
  see: http://zfsonlinux.org/msg/ZFS-8000-4J
config:

       rpool                    DEGRADED
         mirror-0               DEGRADED
           sdd3                 ONLINE
           4085699320674626661  UNAVAIL</pre>

Bam ! It found the partition on the disk! (/dev/sdd3) This also tells me that it is in fact a ZFS mirror setup. Now once we found it, we can simply import it, this is done with **zpool import <name_dataset>**

<pre>root@svennd:~# zpool import rpool
cannot import 'rpool': a pool with that name already exists
use the form 'zpool import &lt;pool | id&gt; &lt;newpool&gt;' to give it a new name</pre>

Woops. Good thing, ZFS helps us out here :

<pre>root@svennd:~# zpool import rpool temp_rpool
cannot import 'rpool': pool may be in use from other system, it was last accessed by (none) (hostid: 0xa8c00802) on Tue Mar 31 15:58:04 2015
use '-f' to import anyway</pre>

Oke, ZFS likes -f, so lets add it ; **zpool import -f poolname new_poolname**

<pre>root@svennd:~# zpool import -f rpool temp_rpool
cannot mount '/': directory is not empty</pre>

The error on there means this was mounted on / (root) before, this obv. is not empty so it does not mount it. To change that first look up what the current mountpoint is, and change that, this can all be done using **zfs get** and **zfs set**.

<pre>root@svennd:~# zfs get all temp_rpool |grep mountpoint
temp_rpool  mountpoint            /                   default
root@svennd:~# zfs set mountpoint=/mnt/datadisk temp_rpool</pre>

Once that's done you can mount the ZFS pool as using **zfs mount.**

<pre>zfs mount temp_rpool</pre>

Another "un-mountable" partition beats the dust. Good luck!

 [1]: https://www.svennd.be/mount-unknown-filesystem-type-ntfs-on-centos-7-2/
 [2]: https://www.svennd.be/mount-unknown-filesystem-nfs-proxmox/
 [3]: https://www.svennd.be/mount-unknown-filesystem-type-lvm2_member/
 [4]: https://www.svennd.be/installing-zfs-on-mint-17-3-live-medium/
 [5]: https://github.com/zfsonlinux/zfs/wiki/Getting-Started
 [6]: http://zfsonlinux.org