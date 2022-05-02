---
title: ZFS on linux version check
author: svennd

date: 2016-01-20T13:32:44+00:00
url: /zfs-on-linux-version-check/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - zfs

---
I'm so hooked on Centos lately I have no clue how to find the version of ZFS installed on a proxmox (debian based) OS. A "hacky" way is checking dmesg during module load but I'm sure an easier solution must exist.

<pre>dmesg | grep ZFS

# if nothing, then perhaps it was not loaded (?)
modprobe zfs</pre>

result :

<pre>dmesg | grep ZFS
[0.000000] Command line: BOOT_IMAGE=/ROOT/pve-1@/boot/vmlinuz-4.2.6-1-pve root=ZFS=/ROOT/pve-1 ro boot=zfs root=ZFS=rpool/ROOT/pve-1 boot=zfs quiet
[0.000000] Kernel command line: BOOT_IMAGE=/ROOT/pve-1@/boot/vmlinuz-4.2.6-1-pve root=ZFS=/ROOT/pve-1 ro boot=zfs root=ZFS=rpool/ROOT/pve-1 boot=zfs quiet
[1.592028] ZFS: Loaded module v0.6.5.3-1, ZFS pool version 5000, ZFS filesystem version 5
</pre>

&nbsp;

Alternative one :

<pre>cat /sys/module/zfs/version
0.7.12-1
</pre>

Alternative two :

<pre>modinfo zfs
filename:       /lib/modules/3.10.0-957.1.3.el7.x86_64/extra/zfs.ko.xz
version:        0.7.12-1
license:        CDDL
author:         OpenZFS on Linux
description:    ZFS
retpoline:      Y
rhelversion:    7.6
...</pre>

&nbsp;