---
title: 'mount: unknown filesystem type ‘linux_raid_member’'
author: svennd

date: 2019-11-06T20:43:05+00:00
url: /mount-unknown-filesystem-type-linux_raid_member/
thumbnail: /img/2020/05/blue.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - filesystem

---
This is a mount issue, that I haven't faced lately anymore; It was however in my drafts for so long; I might as wel write it up;

_linux\_raid\_member_ is a filesystem that is part of a software [RAID][1] meaning you need at least two disks to read the data in a "normal" state. So it largely depends on what RAID level was setup and how many disks where involved.  Most common RAID types are 0, 1, 5, 6, 10. RAID 0, you need all disks, RAID 1 you could access the data with only half of all the disks, with RAID 5, you need all disks - 1; raid 6 you need all disk -2; raid 10 you need half of all disks, but you need the full set.

You might not know what kind of RAID level was picked, lucky you can examine that from the metadata, using mdadm :

<pre># mdadm --examine /dev/sdc1
          Magic :
        Version : 
    Feature Map :
     Array UUID : 
           Name : 
  Creation Time : 
     Raid Level : raid1
   Raid Devices : 2

- snap -</pre>

This learns us that there where only 2 devices and the level was RAID 1; meaning all data is on both disks, and should be equal !

We can start the raid again using mdadm :

<pre>mdadm --assemble --run /dev/md0 /dev/sdc1 --force</pre>

note that we need to use force as mdadm will rightfully complain about the raid being degraded (its missing a disk).

This should generate /dev/md0 which we can mount normally :

<pre>mount /dev/md0 /mnt/external_disk</pre>

useful ["cheat sheet " for mdadm][2] by ducea

&nbsp;

 [1]: https://en.wikipedia.org/wiki/RAID
 [2]: http://www.ducea.com/2009/03/08/mdadm-cheat-sheet/