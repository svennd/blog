---
title: ZFS replace a broken disk
author: svennd

date: 2017-03-28T09:34:07+00:00
url: /zfs-replace-a-broken-disk/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - SysAdmin
  - ZFS
tags:
  - zfs

---
After a reboot, I got greeted with this error :

```
root@server:~# zpool status
  pool: rpool
 state: DEGRADED
status: One or more devices could not be used because the label is missing or
        invalid.  Sufficient replicas exist for the pool to continue
        functioning in a degraded state.
action: Replace the device using 'zpool replace'.
   see: http://zfsonlinux.org/msg/ZFS-8000-4J
  scan: resilvered 10.1G in 0h26m with 0 errors on Mon Mar 27 16:35:05 2017
config:

        NAME                     STATE     READ WRITE CKSUM
        rpool                    DEGRADED     0     0     0
          mirror-0               DEGRADED     0     0     0
            sda                  ONLINE       0     0     0
            2748060340541772838  UNAVAIL      0     0     0  was /dev/sdb2
            sdc2                 ONLINE       0     0     0
            sdd2                 ONLINE       0     0     0
```

I replaced the disk, and then forced a replace.

```zpool replace -f rpool 2748060340541772838 /dev/sdb```

zpool replace [-f] $poolname $old\_device $new\_device

The status is now :

```root@server:~# zpool list
NAME    SIZE  ALLOC   FREE  EXPANDSZ   FRAG    CAP  DEDUP  HEALTH  ALTROOT
rpool   928G  10.2G   918G     16.0E     3%     1%  1.00x  DEGRADED  -
root@server:~# zpool status
  pool: rpool
 state: DEGRADED
status: One or more devices is currently being resilvered.  The pool will
        continue to function, possibly in a degraded state.
action: Wait for the resilver to complete.
  scan: resilver in progress since Tue Mar 28 11:31:57 2017
    14.3M scanned out of 10.2G at 1.02M/s, 2h50m to go
    14.2M resilvered, 0.14% done
config:

        NAME                       STATE     READ WRITE CKSUM
        rpool                      DEGRADED     0     0     0
          mirror-0                 DEGRADED     0     0     0
            sda                    ONLINE       0     0     0
            replacing-1            UNAVAIL      0     0     0
              2748060340541772838  UNAVAIL      0     0     0  was /dev/sdb2
              sdb                  ONLINE       0     0     0  (resilvering)
            sdc2                   ONLINE       0     0     0
            sdd2                   ONLINE       0     0     0

errors: No known data errors
```

It might be required to offline the device first :
```
zpool offline rpool /dev/sda
```

To find what device is broken, one can use ledctl : 
```
ledctl locate = /dev/sda
ledctl locate_off = /dev/sda
```

sources : 
- [zfs offline](https://openzfs.github.io/openzfs-docs/man/8/zpool-offline.8.html)
- [zfs replace](https://openzfs.github.io/openzfs-docs/man/8/zpool-replace.8.html)
