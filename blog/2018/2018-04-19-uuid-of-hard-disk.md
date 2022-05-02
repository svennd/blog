---
title: UUID of hard disk
author: svennd

date: 2018-04-19T13:14:04+00:00
url: /uuid-of-hard-disk/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - filesystem

---
If you ever have been bitten by the changing letters from Linux hard drives, then stop the crying, UUID is here to save the day. Definitely if you fubar'd your installation for the second time and had to reinstall once again, this is a useful tip. Hard disks and virtual raid disks have a UUID, _**U**niversally **U**nique **ID**entifier._

You can use them to access those drives, in /etc/fstab instead of /dev/sd* names;

You can use the UUID by poking the disk the old fasion way :

<pre>blkid /dev/sda1</pre>

This returns :

<pre>[root@server~]# blkid /dev/sda1
/dev/sda1: UUID="696b8eba-f884-4a0a-8f55-b90df6c56b50" TYPE="xfs" PARTLABEL="primary" PARTUUID="6d0f4e74-2929-4a81-9821-dc318013265c"
</pre>

This can be added in /etc/fstab instead of /dev/sda1, which can change if you remove or add another hard disk (it can even change between reboots)

<pre>/dev/sda1 /data                 xfs defaults 0 0</pre>

Can become :

<pre>UUID=696b8eba-f884-4a0a-8f55-b90df6c56b50 /data                 xfs defaults 0 0</pre>

This works, irrelevant if you change hardware or not. (well not when the disk gets replaced!)

_note : beside blkid UUID can also be found in the directory structure by /dev/disk/by-uuid I am however uncertain if this gets populated in less good circumstances (such as grub/recovery/emergency boots)_

<pre>[root@server ~]# ls -l /dev/disk/by-uuid/
total 0
lrwxrwxrwx. 1 root root 10 Apr 19 11:06 265fc00c-2192-46c0-9289-5fc87221d775 -&gt; ../../sdb5
lrwxrwxrwx. 1 root root 10 Apr 19 11:06 2fae283e-98ec-48ea-88c0-226f8c245900 -&gt; ../../sdb4
lrwxrwxrwx. 1 root root 10 Apr 19 11:06 64D3-DD1F -&gt; ../../sdb1
lrwxrwxrwx. 1 root root 10 Apr 19 11:06 696b8eba-f884-4a0a-8f55-b90df6c56b50 -&gt; ../../sda1
lrwxrwxrwx. 1 root root 10 Apr 19 11:06 a2836a81-333a-4c3a-a383-c16e56a4350f -&gt; ../../sdb2
lrwxrwxrwx. 1 root root 10 Apr 19 11:06 c81ca887-d710-4bb4-ab3f-eb38517eadff -&gt; ../../sdb3
</pre>

&nbsp;