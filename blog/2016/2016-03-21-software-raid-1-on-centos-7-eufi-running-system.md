---
title: software RAID 1 on Centos 7, EUFI running system
author: svennd

date: 2016-03-21T10:39:41+00:00
url: /software-raid-1-on-centos-7-eufi-running-system/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Ow boy, two days of my life I'm never getting back. There are some tutorials on how to make a raid1 on a running system, but most of them are clearly copy-paste work and untested, some of them even manage to forget to copy partitions from running system to software raid... ([wtf][1]?)  Lets say they are "outdated"... Anyways this is how you could set it up. I'm not taking 100% credit though, I needed a little help from the [Centos Community][2], thanks guys!

**Installation**

I'm not familiar with lvm's, while initially I tried, using a guide (I can't find anymore) this resulted in a copy (_pvmove_) that was to small by a few blocks ... I had no clue how to fix it (clearly some rounding error, as the devices are identical), and so I decided to go with the good old partitions. Since this is an EUFI setup you need a vfat partiton for the efi pieces. By default the installer comes up with :

  * /home : user data
  * /root : system data
  * /boot : boot data
  * /boot/efi : efi data (grub)
  * swap

I don't need /home on a separate partition cause this is a storage device, so only admin users, and none should store stuff there. So I removed /home partition ending up with : /root /boot /boot/efi and swap. If you have /home , change the method to include this /dev/md*

Important note : /boot/efi can't be in a software raid because of the EUFI restrictions.

**Copy layout**

I copy'd the layout using sgdisk, you could do this manually as well, but I'm lazy. (part of **gdisk** package)

<pre>sgdisk --replicate=/dev/sda /dev/sdb</pre>

As far as I understood this only copy's the partition layout not the data itself.  The both disk should now have exact the same layout :

<pre>[root@server svenn]# fdisk -l /dev/sda1 /dev/sdb1
WARNING: fdisk GPT support is currently new, and therefore in an experimental phase. Use at your own discretion.

Disk /dev/sda: 400.1 GB, 400088457216 bytes, 781422768 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: gpt


#         Start          End    Size  Type            Name
 1         2048       411647    200M  EFI System      EFI System Partition
 2       411648      1435647    500M  Microsoft basic
 3      1435648      9824255      4G  Linux swap
 4      9824256    114681855     50G  Microsoft basic
WARNING: fdisk GPT support is currently new, and therefore in an experimental phase. Use at your own discretion.

Disk /dev/sdb: 400.1 GB, 400088457216 bytes, 781422768 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: gpt


#         Start          End    Size  Type            Name
 1         2048       411647    200M  EFI System      EFI System Partition
 2       411648      1435647    500M  Microsoft basic
 3      1435648      9824255      4G  Linux swap
 4      9824256    114681855     50G  Microsoft basic</pre>

The UUID's which Centos uses, are now identical, to change this we can once again use sgdisk :

<pre>sgdisk -G /dev/sdb</pre>

To check the UUID's use _blkid_ :

<pre>blkid /dev/sd*</pre>

**Create the soft raid 1 using mdadm**

Now lets create the raid, this should be similar for larger raid sets (raid 5,6,10,...) the disk we are using we "set" as missing, the other one we are going to add. Before we can add disks to a /dev/md* device we need to change the partition type to _linux raid auto (type fd)_ or in my case this was _Linux RAID (type 13). _

<pre>fdisk -l /dev/sda2
fdisk -l /dev/sda3
fdisk -l /dev/sda4
</pre>

Changing is done by  "t" (change a partition's system id) then you can check what you need using "L". (13 or fd for older systems (?)) You end with "w" to write it to partition. Once that is done you should see something like :

<pre>[root@huginn svenn]# fdisk -l /dev/sdb
WARNING: fdisk GPT support is currently new, and therefore in an experimental phase. Use at your own discretion.

Disk /dev/sdb: 400.1 GB, 400088457216 bytes, 781422768 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: gpt


#         Start          End    Size  Type            Name
 1         2048       411647    200M  EFI System      EFI System Partition
 2       411648      1435647    500M  Linux RAID
 3      1435648      9824255      4G  Linux RAID
 4      9824256    114681855     50G  Linux RAID
</pre>

now to create the /dev/md devices :

<pre>mdadm --create /dev/md0 --level=1 --raid-disks=2 missing /dev/sdb2
mdadm --create /dev/md1 --level=1 --raid-disks=2 missing /dev/sdb3
mdadm --create /dev/md2 --level=1 --raid-disks=2 missing /dev/sdb4
</pre>

Then to make the partitions on these devices :

<pre>mkfs.ext3 /dev/md0
mkswap /dev/md1
mkfs.xfs /dev/md2</pre>

Now store the current setup :

<pre>mdadm --examine --scan &gt; /etc/mdadm.conf</pre>

the content looks like :

<pre>ARRAY /dev/md/0  metadata=1.2 UUID=4266c365:e258af0b:6b260578:99a530d3 name=server.be:0
ARRAY /dev/md/1  metadata=1.2 UUID=83cf547c:bb8851ff:c4c66734:e9484261 name=server.be:1
ARRAY /dev/md/2  metadata=1.2 UUID=048559bb:b27088f5:388eb6a4:136cc313 name=server.be:2
</pre>

**selinux**

I only found out after I already gone through most, but by default selinux is on in Centos 7, thats good for security, but a problem if a new copy of all files suddenly wanne start running. I hit upon a _permission denied /bin/bash _ as a result. To get around that, make sure you create a empty file in the partition, this will relabel the filesystem :

<pre>touch /.autorelabel</pre>

**Change boot setup**

We need to change the way the system boots, this happens largely in <code class="EnlighterJSRAW" data-enlighter-language="null">/boot/efi/EFI/centos/grub.cfg</code> and in /etc/fstab.

First get the right UUID's for the softraid devices.

<pre>blkid /dev/md/*
/dev/md/0: UUID="6cf4ebb5-0b6a-42fa-9de5-01f1a1df0c74" TYPE="ext3"
/dev/md/1: UUID="3649920c-ac50-4ad6-9860-8885935f1955" TYPE="swap"
/dev/md/2: UUID="c0d03edb-0917-46f7-8675-2779e37b8369" TYPE="xfs"
</pre>

Change /etc/fstab to use the correct devices : (for EUFI systems, ignore /boot/efi for now)

<pre>#
# /etc/fstab
# Created by anaconda on Fri Mar 18 15:36:47 2016
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
#UUID=7d4fd005-c2f0-47e1-94bc-70d18d5523e7 /                       xfs     defaults        0 0
#UUID=f5b3a0ed-a61e-4998-aaac-91cf0ff7921b /boot                   xfs     defaults        0 0
UUID=27C9-4CA5          /boot/efi               vfat    umask=0077,shortname=winnt 0 0
#UUID=4e1f972d-aa06-4e33-8de5-ed4b2e701f0b swap                    swap    defaults        0 0

# new
UUID=c0d03edb-0917-46f7-8675-2779e37b8369 /                       xfs     defaults        0 0
UUID=6cf4ebb5-0b6a-42fa-9de5-01f1a1df0c74 /boot                   ext3     defaults        0 0
UUID=3649920c-ac50-4ad6-9860-8885935f1955  swap                    swap    defaults        0 0</pre>

Now it gets a bit fuzzy, but as far as I understand we should remake the initramfs to include some raid modules, although some people say its not needed, I just ran it anyways :

<pre>dracut --mdadmconf --fstab --add="dm dmraid mdraid" --add-drivers="raid1" --force /boot/initramfs-$(uname -r).img $(uname -r) -M</pre>

After that its time to update the grub2 configuration :  add in /etc/default/grub "rd.auto rd.auto=1" to make sure the /dev/md* are made during boot. The final version looks :

<pre>[root@server svenn]# cat /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="rhgb quiet rd.auto rd.auto=1"
GRUB_DISABLE_RECOVERY="true"</pre>

Then we can regenerate he grub using :

<pre>grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg</pre>

output :

<pre>[root@huginn svenn]# grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.10.0-327.10.1.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-327.10.1.el7.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-327.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-327.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-fc999fa1c8a9468db83bdcae3a95d9cb
Found initrd image: /boot/initramfs-0-rescue-fc999fa1c8a9468db83bdcae3a95d9cb.img
find: '/var/lib/os-prober/mount/EFI': Input/output error
find: '/var/lib/os-prober/mount/EFI': Input/output error
done</pre>

**Copy the data**

note : Now you can't change anything anymore on the system, or it won't be included in the new location mount the devices md0 and md2 (/boot and /root) on /mnt/md*

<pre>mkdir /tmp/md0; mount /dev/md0 /tmp/md0
mkdir /tmp/md2; mount /dev/md2 /tmp/md2</pre>

next , copy the data : (there might be better/cleaner methods)

<pre>cd /boot ; rsync -av --progress --exclude=efi* . /mnt/md0/
cd / ; find . -depth -xdev | grep -v '^\./tmp/' | cpio -pmd /tmp/md2
sync</pre>

Now our system has been updated to be able to boot in raid 1 mode with 1 missing disk; Its time to reboot! (and pray everything works!)

**You survived the reboot ?**

Congratz ! now only to add the disk members to the raid 1 :

<pre>mdadm --add /dev/md0 /dev/sda2
mdadm --add /dev/md1 /dev/sda3
mdadm --add /dev/md2 /dev/sda4
</pre>

check cat /proc/mdstat to see the process of rebuild.

**(optional) raid efi**

Now to have no single point of failure we could also add the second partition that has efi to the system, and hence have 2 efi partitions. This is done by :

copy the content of /boot/efi to /boot/efi2:

<pre>mkdir /boot/efi2 ; mount /dev/sdb2 /boot/efi2
cp -r /boot/efi /boot/efi2</pre>

remake the config of grub :

<pre>grub2-mkconfig -o /boot/efi2/EFI/centos/grub.cfg</pre>

Then add it to the /etc/fstab (blkid /dev/sdb2)

<pre>UUID=27C9-4CA5          /boot/efi               vfat    umask=0077,shortname=winnt 0 0
UUID=E0EA-375E          /boot/efi2              vfat    umask=0077,shortname=winnt 0 0
</pre>

finally add it to efibootmgr :

<pre>efibootmgr -c -d /dev/sdb -p 1 -L "CentOS-AltDrv" -l '\EFI\centos\shim.efi'</pre>

Bam, that's how you make a running RAID 1 system from within... 🙂

 [1]: https://www.howtoforge.com/how-to-set-up-software-raid1-on-a-running-system-incl-grub-configuration-centos-5.3-p3
 [2]: https://www.centos.org/forums/viewtopic.php?f=47&t=57068