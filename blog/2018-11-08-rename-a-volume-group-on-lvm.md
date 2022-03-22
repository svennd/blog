---
title: Rename a volume group on LVM
author: svennd

date: 2018-11-08T14:57:17+00:00
url: /rename-a-volume-group-on-lvm/
thumbnail: /img/2020/05/krzysztof-kowalik-ciyfSzBmaKU-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - filesystem

---
In a recovery of a system I found myself in annoying pickle of having two disks with a similar LVM layout and more precise with a equally named volume group. This can cause issues, however in my case it just stopped me from mounting the original device. To make matters worse the sizes of the volumes where nearly identically in size. Not really a user of LVM, it was time for google to save me.

Activating both identical volume failed :

<pre>root@svennd:~# vgchange -ay
  device-mapper: create ioctl on pve-swapLVM-Do7QVk4UeQXTTqVtB5b0pISJQy1YL1YJ4RB7lZXsAvYVGcYs9e7TZcFuHfkUBZJz failed: Device or resource busy
  device-mapper: create ioctl on pve-rootLVM-Do7QVk4UeQXTTqVtB5b0pISJQy1YL1YJgnTssDJJQj59LISgspFSLEH2pzqQLYw9 failed: Device or resource busy
  1 logical volume(s) in volume group "pve" now active
  device-mapper: create ioctl on pve-dataLVM-46zXzJmc3xbdOtJdsVXrSlb5siaAacfRmhJ3Ac2ET7201zotB4DEX2kVu9gSafDC-tpool failed: Device or resource busy
  2 logical volume(s) in volume group "pve" now active</pre>

So first thing I found was _lvmdiskscan_ this will search for disk that are formatted with LVM. Not surprisingly I found two disks (new and old one) and a third data disk. (RAID)

<pre>root@svennd:~# lvmdiskscan
  /dev/sda2 [     256.00 MiB]
  /dev/sda3 [     465.51 GiB] LVM physical volume
  /dev/sdb2 [     510.00 MiB]
  /dev/sdb3 [     465.15 GiB] LVM physical volume
  /dev/sdc  [      29.10 TiB]
  1 disk
  2 partitions
  0 LVM physical volume whole disks
  2 LVM physical volumes</pre>

_note : partition /dev/sda3 and /dev/sdb3 are dangerously similar !_

Now we know that the partitions are very similar, however perhaps I might be lucky that the logical volumes where different _lvscan :_

<pre>root@svennd:~# lvscan
  inactive          '/dev/pve/swap' [58.12 GiB] inherit
  inactive          '/dev/pve/root' [96.00 GiB] inherit
  ACTIVE            '/dev/pve/data' [295.03 GiB] inherit
  ACTIVE            '/dev/pve/swap' [8.00 GiB] inherit
  ACTIVE            '/dev/pve/root' [96.00 GiB] inherit
  inactive          '/dev/pve/data' [338.60 GiB] inherit</pre>

_note : there is a slight different in sizing of the volumes, this is good information. However the names are equal. This is causing the issue._

If we can change the volume group name (_pve)_ this should be enough to mount it. However I don't want to take the active ones, as that might crash my current system (?) So let's check using _vgdisplay :_

<pre>root@svennd:~# vgdisplay
  --- Volume group ---
  VG Name               pve
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  64
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                3
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               465.15 GiB
  PE Size               4.00 MiB
  Total PE              119078
  Alloc PE / Size       114983 / 449.15 GiB
  Free  PE / Size       4095 / 16.00 GiB
  VG UUID               Do7QVk-4UeQ-XTTq-VtB5-b0pI-SJQy-1YL1YJ

  --- Volume group ---
  VG Name               pve
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  7
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                3
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               465.51 GiB
  PE Size               4.00 MiB
  Total PE              119170
  Alloc PE / Size       115075 / 449.51 GiB
  Free  PE / Size       4095 / 16.00 GiB
  VG UUID               46zXzJ-mc3x-bdOt-JdsV-XrSl-b5si-aAacfR</pre>

Since there is a slight difference in size (449.51 vs 449.15) and I know the current disk in use is my new system (/dev/sda)  (check using _df -h_).

I know I need /dev/sdb3 with 465.15 GB in size, this is the first in the list with UUID (Do7QVk-4UeQ-XTTq-VtB5-b0pI-SJQy-1YL1YJ)

Finally we know what we want to rename, it seems almost too simple using _vgrename_ :

<pre>root@svennd:~# vgrename Do7QVk-4UeQ-XTTq-VtB5-b0pI-SJQy-1YL1YJ pveb
  Processing VG pve because of matching UUID Do7QVk-4UeQ-XTTq-VtB5-b0pI-SJQy-1YL1YJ
  Volume group "Do7QVk-4UeQ-XTTq-VtB5-b0pI-SJQy-1YL1YJ" successfully renamed to "pveb"</pre>

After this we can activate the volume group using _vgchange :_

<pre>vgchange -ay</pre>

And voila one can mount the new device :

<pre>mkdir -p /data/oldroot
mkdir -p /data/data

mount /dev/pveb/root /data/oldroot
mount /dev/pveb/data /data/data</pre>

Tadaaa ! The data is available once more 🙂