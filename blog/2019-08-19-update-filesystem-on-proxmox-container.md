---
title: update filesystem on proxmox container
author: svennd

date: 2019-08-19T12:08:47+00:00
url: /update-filesystem-on-proxmox-container/
thumbnail: /img/2020/02/nightsky.png
categories:
  - Linux
  - SysAdmin
tags:
  - proxmox

---
A quick fix for a weird issue that in Proxmox unprivileged container fails to install filesystem update on Centos 7 :

<pre>[root@server ~]# yum install filesystem
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: ftp.belnet.be
 * extras: ftp.belnet.be
 * updates: ftp.belnet.be
Resolving Dependencies
--&gt; Running transaction check
---&gt; Package filesystem.x86_64 0:3.2-21.el7 will be updated
---&gt; Package filesystem.x86_64 0:3.2-25.el7 will be an update
--&gt; Finished Dependency Resolution

Dependencies Resolved

===================================================================================================================================================================================================================
 Package                                              Arch                                             Version                                                Repository                                      Size
===================================================================================================================================================================================================================
Updating:
 filesystem                                           x86_64                                           3.2-25.el7                                             base                                           1.0 M

Transaction Summary
===================================================================================================================================================================================================================
Upgrade  1 Package

Total download size: 1.0 M
Is this ok [y/d/N]: y
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
filesystem-3.2-25.el7.x86_64.rpm                                                                                                                                                            | 1.0 MB  00:00:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : filesystem-3.2-25.el7.x86_64                                                                                                                                                                    1/2
Error unpacking rpm package filesystem-3.2-25.el7.x86_64
error: unpacking of archive failed on file /sys: cpio: chown
  Verifying  : filesystem-3.2-25.el7.x86_64                                                                                                                                                                    1/2
filesystem-3.2-21.el7.x86_64 was supposed to be removed but is not!
  Verifying  : filesystem-3.2-21.el7.x86_64                                                                                                                                                                    2/2

Failed:
  filesystem.x86_64 0:3.2-21.el7                                                                           filesystem.x86_64 0:3.2-25.el7

Complete!
</pre>

The fix, thanks to [this post][1] :

<pre>echo "%_netsharedpath /sys:/proc" &gt;&gt; /etc/rpm/macros.dist</pre>

and tada :

<pre>Updated:
  filesystem.x86_64 0:3.2-25.el7</pre>

&nbsp;

 [1]: https://forum.proxmox.com/threads/cant-update-centos7-or-install-httpd-in-unprivileged-lxc-container.31174/#post-155952