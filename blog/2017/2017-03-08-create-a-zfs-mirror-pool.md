---
title: Create a ZFS mirror pool
author: svennd

date: 2017-03-08T20:03:56+00:00
url: /create-a-zfs-mirror-pool/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - SysAdmin
  - ZFS
tags:
  - zfs

---
I recently revived a "old" compute cluster. While its hardware was formidable back in the day, now it has been replaced with a younger version, which has more compute nodes. I wanted to create a data partition of two disks, normally I have a raid controller but on this server, there is none.  An open invitation for a software RAID it is, so I could go with _mdadm_ as software raid, but I did not want to read up on all those commands (again). When in fact ZFS is already in my head.

Creating a mirror or RAID 1 can be done using : _zpool create $poolname mirror $first\_disk $second\_disk  
_ This would make a mirror and mount it on /data, now that is the theory, in practice you most likely will be shown this error :

<pre>invalid vdev specification
use '-f' to override the following errors:
/dev/sdb does not contain an EFI label but it may contain partition information in the MBR.
/dev/sdc does not contain an EFI label but it may contain partition information in the MBR.</pre>

So be sure to add _-f_ , it is however a good idea to double check if those are the disks you want to use. Note that the content on both disks will be destroyed during this process.

<pre>zpool create -f data mirror /dev/sdb /dev/sdc</pre>

After that you can see

<pre>[root@lserver-01 ~]# zfs list
NAME   USED  AVAIL  REFER  MOUNTPOINT
data   216K  3.51T    96K  /data
[root@server-01 ~]# zpool status
  pool: data
 state: ONLINE
  scan: none requested
config:

        NAME        STATE     READ WRITE CKSUM
        data        ONLINE       0     0     0
          mirror-0  ONLINE       0     0     0
            sdb     ONLINE       0     0     0
            sdc     ONLINE       0     0     0

errors: No known data errors</pre>

One of the tuning options for pools is _ashift, _which can be 9 (for 512 sector drives) or 12 (for 4k sector). However, this can only be set at creation. This can be done using the option _-o ashift=value_ so why did I not tell you ? Cause ZOL (ZFS on Linux) since [a while, will try][1] and find the correct value.  From my finding (on the internet) almost all disks these days are 4k sector drives or _[advanced format][2] drives_. This you can check using _hdparm -i /dev/sdb_ (you might need to install this)

<pre>[root@server ~]# hdparm -I /dev/sdb
        Model Number:       WDC WD4Y0                  
        Firmware Revision:  80.00A80
        Transport:          Serial, SATA 1.0a, SATA II Extensions, SATA Rev 2.5, SATA Rev 2.6, SATA Rev 3.0
Standards:
        Supported: 9 8 7 6 5 
        Likely used: 9
Configuration:
        Logical         max     current
        cylinders       16383   16383
        heads           16      16
        sectors/track   63      63
        --
        CHS current addressable sectors:   16514064
        LBA    user addressable sectors:  268435455
        LBA48  user addressable sectors: 7814037168
        Logical  Sector size:                   512 bytes
        Physical Sector size:                  4096 bytes
        Logical Sector-0 offset:                  0 bytes
        device size with M = 1024*1024:     3815447 MBytes
        device size with M = 1000*1000:     4000787 MBytes (4000 GB)</pre>

As you see the Logical sector size, is 512 bytes, this is for backwards compatibility, but the physical sector size is 4k. So in this situation a ashift=12 would be ideal. You can verify what your ashift is, by using the _zdb_ tool :

<pre>[root@lungo-01 ~]# zdb | grep ashift
            ashift: 12</pre>

From what I read in the repo, it seems that 512 bytes in some cases can give you more storage if you have allot of very tiny files, compared to 4k, but that 4k is in almost all cases allot more performant. In general terms unless you really have a corner case, default ZFS will most likely guess the best option.

After creating this pool, I would recommend you read up on [basic tuning][3], in short :

<pre>zfs set xattr=sa data
zfs set acltype=posixacl data
zfs set compression=lz4 data
zfs set atime=off data
zfs set relatime=off data</pre>

And that's it folks !

 [1]: https://github.com/zfsonlinux/zfs/issues/967
 [2]: https://en.wikipedia.org/wiki/Advanced_Format
 [3]: https://www.svennd.be/basic-zfs-tune-tips/