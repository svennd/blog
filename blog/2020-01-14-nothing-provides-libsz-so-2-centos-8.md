---
title: nothing provides libsz.so.2 Centos 8
author: svennd

date: 2020-01-14T13:43:06+00:00
url: /nothing-provides-libsz-so-2-centos-8/
thumbnail: /img/2020/04/centos.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos

---
While installing **hdf5** I got this error :

<pre># yum install hdf5.x86_64
Last metadata expiration check: 1:05:47 ago on Tue 14 Jan 2020 01:27:49 PM CET.
Error:
 Problem: conflicting requests
  - nothing provides libsz.so.2()(64bit) needed by hdf5-1.10.5-4.el8.x86_64
(try to add '--skip-broken' to skip uninstallable packages or '--nobest' to use not only best candidate packages)</pre>

As it turns out, I'm not the [only][1] [one][2]. Turns out that installing epel-release does not cut it any longer; You need to install the PowerTools repo:

<pre>dnf config-manager --set-enabled PowerTools
yum update</pre>

After that; It installed fine :

<pre>yum install hdf5.x86_64                                      Last metadata expiration check: 0:00:09 ago on Tue 14 Jan 2020 02:37:30 PM CET.
Dependencies resolved.
==========================================================================================
 Package              Arch            Version                   Repository           Size
==========================================================================================
Installing:
 hdf5                 x86_64          1.10.5-4.el8              epel                2.1 M
Installing dependencies:
 libgfortran          x86_64          8.2.1-3.5.el8             BaseOS              636 k
 libquadmath          x86_64          8.2.1-3.5.el8             BaseOS              167 k
 libaec               x86_64          1.0.2-3.el8               PowerTools           39 k

Transaction Summary
==========================================================================================
Install  4 Packages

Total download size: 3.0 M
Installed size: 12 M
Is this ok [y/N]: y
Downloading Packages:
(1/4): libaec-1.0.2-3.el8.x86_64.rpm                      1.4 MB/s |  39 kB     00:00
(2/4): libquadmath-8.2.1-3.5.el8.x86_64.rpm               4.0 MB/s | 167 kB     00:00
(3/4): libgfortran-8.2.1-3.5.el8.x86_64.rpm               8.2 MB/s | 636 kB     00:00
(4/4): hdf5-1.10.5-4.el8.x86_64.rpm                        11 MB/s | 2.1 MB     00:00
------------------------------------------------------------------------------------------
Total                                                     938 kB/s | 3.0 MB     00:03
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                  1/1
  Installing       : libaec-1.0.2-3.el8.x86_64                                        1/4
  Installing       : libquadmath-8.2.1-3.5.el8.x86_64                                 2/4
  Running scriptlet: libquadmath-8.2.1-3.5.el8.x86_64                                 2/4
  Installing       : libgfortran-8.2.1-3.5.el8.x86_64                                 3/4
  Running scriptlet: libgfortran-8.2.1-3.5.el8.x86_64                                 3/4
  Installing       : hdf5-1.10.5-4.el8.x86_64                                         4/4
  Running scriptlet: hdf5-1.10.5-4.el8.x86_64                                         4/4
  Verifying        : libgfortran-8.2.1-3.5.el8.x86_64                                 1/4
  Verifying        : libquadmath-8.2.1-3.5.el8.x86_64                                 2/4
  Verifying        : libaec-1.0.2-3.el8.x86_64                                        3/4
  Verifying        : hdf5-1.10.5-4.el8.x86_64                                         4/4

Installed:
  hdf5-1.10.5-4.el8.x86_64                    libgfortran-8.2.1-3.5.el8.x86_64
  libquadmath-8.2.1-3.5.el8.x86_64            libaec-1.0.2-3.el8.x86_64

Complete!</pre>

&nbsp;

As its written in the [extra not][3]e's :

<pre><b>NOTE for CentOS 8 users</b>
EPEL packages assume that the '<b>PowerTools'</b> repository is enabled.
You can do this with: <code>dnf config-manager --set-enabled PowerTools</code> <b>NOTE for CentOS users</b> You can install EPEL by running <b>yum install epel-release</b>. The package is included in the CentOS Extras repository, enabled by default.</pre>

 [1]: https://bugzilla.redhat.com/show_bug.cgi?id=1751197
 [2]: https://bugzilla.redhat.com/show_bug.cgi?id=1756936
 [3]: https://fedoraproject.org/wiki/EPEL#How_can_I_use_these_extra_packages.3F