---
title: 'Upgrade log ZFS 0.8.6 -> 2.0.5 (Centos 8.4)'
author: svennd

date: 2021-07-12T11:23:27+00:00
url: /upgrade-log-zfs-0-8-6-2-centos-8-4/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - ChromeOS
  - SysAdmin
  - ZFS

---
I start with rebooting the machine to be sure we got the latest kernel & updates active :

<pre>yum update 
reboot</pre>

Remove the zfs-release, as this generates some warning on my system.

<pre>yum remove zfs-release</pre>

The error :

<pre>error: can't create transaction lock on /var/lib/rpm/.rpm.lock (Resource temporarily unavailable)
error: /etc/pki/rpm-gpg/RPM-GPG-KEY-zfsonlinux: key 1 import failed.
warning: %post(zfs-release-1-8.4.noarch) scriptlet failed, exit status 1
</pre>

Install the new release :

<pre>yum install [https://zfsonlinux.org/epel/zfs-release.el8_5.noarch.rpm](https://zfsonlinux.org/epel/zfs-release.el8_5.noarch.rpm)
gpg --quiet --with-fingerprint /etc/pki/rpm-gpg/RPM-GPG-KEY-zfsonlinux
</pre>

Then yum update :

<pre>[root@server ~]# yum update
Last metadata expiration check: 0:01:08 ago on Mon 12 Jul 2021 12:59:56 PM CEST.
Dependencies resolved.
====================================================================================================================================
 Package                          Architecture                 Version                              Repository                 Size
====================================================================================================================================
Installing:
 libnvpair3                       x86_64                       2.0.5-1.el8                          zfs                        37 k
     replacing  libnvpair1.x86_64 0.8.6-1.el8
 libuutil3                        x86_64                       2.0.5-1.el8                          zfs                        31 k
     replacing  libuutil1.x86_64 0.8.6-1.el8
 libzfs4                          x86_64                       2.0.5-1.el8                          zfs                       226 k
     replacing  libzfs2.x86_64 0.8.6-1.el8
 libzpool4                        x86_64                       2.0.5-1.el8                          zfs                       1.2 M
     replacing  libzpool2.x86_64 0.8.6-1.el8
Upgrading:
 zfs                              x86_64                       2.0.5-1.el8                          zfs                       623 k
 zfs-dkms                         noarch                       2.0.5-1.el8                          zfs                       9.2 M

Transaction Summary
====================================================================================================================================
Install  4 Packages
Upgrade  2 Packages

Total download size: 11 M
Is this ok [y/N]: y
Downloading Packages:
(1/6): libnvpair3-2.0.5-1.el8.x86_64.rpm                                                             78 kB/s |  37 kB     00:00
(2/6): libuutil3-2.0.5-1.el8.x86_64.rpm                                                              63 kB/s |  31 kB     00:00
(3/6): libzfs4-2.0.5-1.el8.x86_64.rpm                                                               233 kB/s | 226 kB     00:00
(4/6): zfs-2.0.5-1.el8.x86_64.rpm                                                                   903 kB/s | 623 kB     00:00
(5/6): libzpool4-2.0.5-1.el8.x86_64.rpm                                                             1.2 MB/s | 1.2 MB     00:01
(6/6): zfs-dkms-2.0.5-1.el8.noarch.rpm                                                              8.2 MB/s | 9.2 MB     00:01
------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                               5.4 MB/s |  11 MB     00:02
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                            1/1
  Running scriptlet: libnvpair3-2.0.5-1.el8.x86_64                                                                              1/1
  Installing       : libnvpair3-2.0.5-1.el8.x86_64                                                                             1/12
  Running scriptlet: libnvpair3-2.0.5-1.el8.x86_64                                                                             1/12
  Installing       : libuutil3-2.0.5-1.el8.x86_64                                                                              2/12
  Running scriptlet: libuutil3-2.0.5-1.el8.x86_64                                                                              2/12
  Installing       : libzfs4-2.0.5-1.el8.x86_64                                                                                3/12
  Running scriptlet: libzfs4-2.0.5-1.el8.x86_64                                                                                3/12
  Installing       : libzpool4-2.0.5-1.el8.x86_64                                                                              4/12
  Running scriptlet: libzpool4-2.0.5-1.el8.x86_64                                                                              4/12
  Upgrading        : zfs-dkms-2.0.5-1.el8.noarch                                                                               5/12
  Running scriptlet: zfs-dkms-2.0.5-1.el8.noarch                                                                               5/12
Loading new zfs-2.0.5 DKMS files...
Building for 4.18.0-305.7.1.el8_4.x86_64
Building initial module for 4.18.0-305.7.1.el8_4.x86_64
Done.

zavl.ko.xz: -snip-
znvpair.ko.xz: -snip-
zunicode.ko.xz: -snip-
zcommon.ko.xz: -snip-
zfs.ko.xz: -snip-
icp.ko.xz: -snip-
zlua.ko.xz: -snip-
spl.ko.xz: -snip-
zzstd.ko.xz:
Running module version sanity check.
 - Original module
   - This kernel never originally had a module by this name
 - Installation
   - Installing to /lib/modules/4.18.0-305.7.1.el8_4.x86_64/extra/

depmod....

DKMS: install completed.

  Upgrading        : zfs-2.0.5-1.el8.x86_64                                                                                    6/12
  Running scriptlet: zfs-2.0.5-1.el8.x86_64                                                                                    6/12
  Running scriptlet: zfs-0.8.6-1.el8.x86_64                                                                                    7/12
  Cleanup          : zfs-0.8.6-1.el8.x86_64                                                                                    7/12
  Running scriptlet: zfs-0.8.6-1.el8.x86_64                                                                                    7/12
  Running scriptlet: zfs-dkms-0.8.6-1.el8.noarch                                                                               8/12

Uninstall of zfs module (zfs-0.8.6-1) beginning:

-------- Uninstall Beginning --------
Module:  zfs
Version: 0.8.6
Kernel:  4.18.0-240.1.1.el8_3.x86_64 (x86_64)
-------------------------------------

Status: Before uninstall, this module version was ACTIVE on this kernel.
Removing any linked weak-modules
depmod: ERROR: fstatat(4, icp.ko.xz): No such file or directory
depmod: ERROR: fstatat(4, spl.ko.xz): No such file or directory
depmod: ERROR: fstatat(4, zavl.ko.xz): No such file or directory
depmod: ERROR: fstatat(4, zcommon.ko.xz): No such file or directory
depmod: ERROR: fstatat(4, zfs.ko.xz): No such file or directory
depmod: ERROR: fstatat(4, zlua.ko.xz): No such file or directory
depmod: ERROR: fstatat(4, znvpair.ko.xz): No such file or directory
depmod: ERROR: fstatat(4, zunicode.ko.xz): No such file or directory

zavl.ko.xz: -snip-
znvpair.ko.xz: -snip-
zunicode.ko.xz: -snip-
zcommon.ko.xz: -snip-
zfs.ko.xz: -snip-
icp.ko.xz: -snip-
zlua.ko.xz: -snip-
spl.ko.xz:
 - Uninstallation
   - Deleting from: /lib/modules/4.18.0-240.1.1.el8_3.x86_64/extra/
 - Original module
   - No original module was found for this module on this kernel.
   - Use the dkms install command to reinstall any previous module version.

depmod....

DKMS: uninstall completed.

-------- Uninstall Beginning --------
Module:  zfs
Version: 0.8.6
Kernel:  4.18.0-305.3.1.el8.x86_64 (x86_64)
-------------------------------------

Status: Before uninstall, this module version was ACTIVE on this kernel.
Removing any linked weak-modules

zavl.ko.xz: -snip-
znvpair.ko.xz: -snip-
zunicode.ko.xz: -snip-
zcommon.ko.xz: -snip-
zfs.ko.xz: -snip-
icp.ko.xz: -snip-
zlua.ko.xz: -snip-
spl.ko.xz: 
 - Uninstallation
   - Deleting from: /lib/modules/4.18.0-305.3.1.el8.x86_64/extra/
 - Original module
   - No original module was found for this module on this kernel.
   - Use the dkms install command to reinstall any previous module version.

depmod....

DKMS: uninstall completed.

-------- Uninstall Beginning --------
Module:  zfs
Version: 0.8.6
Kernel:  4.18.0-305.7.1.el8_4.x86_64 (x86_64)
-------------------------------------

Status: This module version was INACTIVE for this kernel.
depmod....

DKMS: uninstall completed.

------------------------------
Deleting module version: 0.8.6
completely from the DKMS tree.
------------------------------
Done.

  Cleanup          : zfs-dkms-0.8.6-1.el8.noarch                                                                               8/12
  Obsoleting       : libzfs2-0.8.6-1.el8.x86_64                                                                                9/12
  Running scriptlet: libzfs2-0.8.6-1.el8.x86_64                                                                                9/12
  Obsoleting       : libzpool2-0.8.6-1.el8.x86_64                                                                             10/12
  Running scriptlet: libzpool2-0.8.6-1.el8.x86_64                                                                             10/12
  Obsoleting       : libnvpair1-0.8.6-1.el8.x86_64                                                                            11/12
  Running scriptlet: libnvpair1-0.8.6-1.el8.x86_64                                                                            11/12
  Obsoleting       : libuutil1-0.8.6-1.el8.x86_64                                                                             12/12
  Running scriptlet: libuutil1-0.8.6-1.el8.x86_64                                                                             12/12
  Running scriptlet: zfs-2.0.5-1.el8.x86_64                                                                                   12/12
  Running scriptlet: libuutil1-0.8.6-1.el8.x86_64                                                                             12/12
  Verifying        : libnvpair3-2.0.5-1.el8.x86_64                                                                             1/12
  Verifying        : libnvpair1-0.8.6-1.el8.x86_64                                                                             2/12
  Verifying        : libuutil3-2.0.5-1.el8.x86_64                                                                              3/12
  Verifying        : libuutil1-0.8.6-1.el8.x86_64                                                                              4/12
  Verifying        : libzfs4-2.0.5-1.el8.x86_64                                                                                5/12
  Verifying        : libzfs2-0.8.6-1.el8.x86_64                                                                                6/12
  Verifying        : libzpool4-2.0.5-1.el8.x86_64                                                                              7/12
  Verifying        : libzpool2-0.8.6-1.el8.x86_64                                                                              8/12
  Verifying        : zfs-2.0.5-1.el8.x86_64                                                                                    9/12
  Verifying        : zfs-0.8.6-1.el8.x86_64                                                                                   10/12
  Verifying        : zfs-dkms-2.0.5-1.el8.noarch                                                                              11/12
  Verifying        : zfs-dkms-0.8.6-1.el8.noarch                                                                              12/12

Upgraded:
  zfs-2.0.5-1.el8.x86_64                                         zfs-dkms-2.0.5-1.el8.noarch
Installed:
  libnvpair3-2.0.5-1.el8.x86_64     libuutil3-2.0.5-1.el8.x86_64     libzfs4-2.0.5-1.el8.x86_64     libzpool4-2.0.5-1.el8.x86_64

Complete!

</pre>

Finally I did a reboot and activated the features :

<pre>[root@server ~] # reboot

[root@server ~] # zpool upgrade poolname
This system supports ZFS pool feature flags.

Enabled the following features on 'poolname':
  redaction_bookmarks
  redacted_datasets
  bookmark_written
  log_spacemap
  livelist
  device_rebuild
  zstd_compress

[root@server ~]zfs version
zfs-2.0.5-1
zfs-kmod-2.0.5-1
</pre>

ZFS upgraded !