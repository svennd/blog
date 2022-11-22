---
author: "Svenn D'Hert"
title: "Partition and format a new disk ext4"
description: ""
date: 2022-11-22
thumbnail: /img/2022/11/luigi-boccardo.jpg
url: /partition_and_format_disk_ext4/
---
A bit silly I still have to google this, but here goes. How to partition and format a disk. In this case I'm formatting a SSD to be used as `/tmp` in a server.


You can find the added disk generally in the logs 

```
# cat /var/log/syslog
Nov 22 15:14:05 server kernel: [13752487.699683] sd 0:0:9:0: [sdi] 3907029168 512-byte logical blocks: (2.00 TB/1.82 TiB)
Nov 22 15:14:05 server kernel: [13752487.700909] sd 0:0:9:0: [sdi] Write Protect is off
Nov 22 15:14:05 server kernel: [13752487.700914] sd 0:0:9:0: [sdi] Mode Sense: 9b 00 10 08
Nov 22 15:14:05 server kernel: [13752487.701705] sd 0:0:9:0: [sdi] Write cache: enabled, read cache: enabled, supports DPO and FUA
Nov 22 15:14:05 server kernel: [13752487.773737] sd 0:0:9:0: [sdi] Attached SCSI disk
Nov 22 15:17:52 server kernel: [13752714.826698]  sdi: sdi1
```
or if you recently added based on the date in `/dev/sd*`
```# ls -l /dev/sd*
brw-rw---- 1 root disk 8,   0 Aug 26 06:11 /dev/sda
brw-rw---- 1 root disk 8,   1 Aug 26 06:11 /dev/sda1
...
brw-rw---- 1 root disk 8, 128 Nov 22 15:17 /dev/sdi
brw-rw---- 1 root disk 8, 129 Nov 22 15:18 /dev/sdi1
```


Format the disk : (as root)
```
# fdisk -l /dev/sdi
n # for new partition
p # for primary
1 # (default) parition number
2048 # (default) start sector
3907029167 # (default) last sector
w # write changes to disk
```

format the disk in ext4 (for example)
```
# mkfs.ext4 /dev/sdi1 
mke2fs 1.45.5 (07-Jan-2020)
Discarding device blocks: done                            
Creating filesystem with 488378390 4k blocks and 122101760 inodes
Filesystem UUID: 6ed1591b-7e79-4b3f-934a-0fa26044212a
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968, 
        102400000, 214990848

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: don
```

Then for mounting as `/tmp` (in this case), we can use `/dev/sdi1` or much safer is using the UUID from the device. Since the UUID never changes. This can be done using blkid.
```
# blkid | grep sdi1
/dev/sdi1: UUID="6ed1591b-7e79-4b3f-934a-0fa26044212a" TYPE="ext4" PARTUUID="179fd4df-01"
```
You can also find this in `/dev/disk/by-uuid/`

Changing `/etc/fstab`

location, mountpoint, filetype, options, backup_operation (legacy), file system check order (0 : no, 1 : root, 2 : "other")
```
/dev/disk/by-uuid/6ed1591b-7e79-4b3f-934a-0fa26044212a /tmp ext4 defaults   0   2
```

Then reboot the device or re-mount using : 
```
mount -a
```

That's how its done :)


img by [lboccardo95](https://unsplash.com/@lboccardo95)