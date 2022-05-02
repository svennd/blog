---
title: Create ZFS Raidz2 pool
author: svennd

date: 2019-03-20T10:14:31+00:00
url: /create-zfs-raidz2-pool/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - SysAdmin
  - ZFS
tags:
  - zfs

---
This is a quick howto, I made a raidz2 pool on ZFS, its very similar to how to [create a mirror][1].

find out what disks you are giving to the pool :

<pre>root@panda:~# fdisk -l /dev/sd* | grep Disk
Disk /dev/sda: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors
Disk /dev/sdb: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors
Disk /dev/sdc: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors
Disk /dev/sdd: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors
Disk /dev/sde: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors
Disk /dev/sdf: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors
Disk /dev/sdg: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors
Disk /dev/sdh: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors
Disk /dev/sdi: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors
Disk /dev/sdj: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors
Disk /dev/sdk: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors
Disk /dev/sdl: 9.1 TiB, 10000831348736 bytes, 19532873728 sectors</pre>

I was lucky that the main system is on a nvme so not /dev/sd* so basically all disks can be in the pool.

Create the pool, the name is panda, and the "raid" level raidz2 meaning 2 disks can fail before data loss occurs similar to RAID6.

<pre>zpool create panda raidz2 /dev/sda /dev/sdb /dev/sdc /dev/sdd /dev/sde /dev/sdf /dev/sdg /dev/sdh /dev/sdi /dev/sdj /dev/sdk /dev/sdl</pre>

After that the pool is made :

<pre>root@panda:~# zfs list
NAME    USED  AVAIL  REFER  MOUNTPOINT
panda   768K  80.4T   219K  /panda
root@panda:~# zpool status
  pool: panda
 state: ONLINE
  scan: none requested
config:

        NAME        STATE     READ WRITE CKSUM
        panda       ONLINE       0     0     0
          raidz2-0  ONLINE       0     0     0
            sda     ONLINE       0     0     0
            sdb     ONLINE       0     0     0
            sdc     ONLINE       0     0     0
            sdd     ONLINE       0     0     0
            sde     ONLINE       0     0     0
            sdf     ONLINE       0     0     0
            sdg     ONLINE       0     0     0
            sdh     ONLINE       0     0     0
            sdi     ONLINE       0     0     0
            sdj     ONLINE       0     0     0
            sdk     ONLINE       0     0     0
            sdl     ONLINE       0     0     0

errors: No known data errors</pre>

Note : you can rename the disks to be sure they are consistent across hardware changes, but it never gave any issues for me personally ...

Don't forget to change the defaults settings to something you prefer :

<pre>zfs set xattr=sa panda
zfs set acltype=posixacl panda
zfs set compression=lz4 panda
zfs set atime=off panda
zfs set relatime=off panda</pre>

See the [basic tuning tips][2] for more info.

 [1]: https://www.svennd.be/create-a-zfs-mirror-pool/
 [2]: https://www.svennd.be/basic-zfs-tune-tips/