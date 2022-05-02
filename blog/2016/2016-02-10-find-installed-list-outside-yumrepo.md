---
title: Find installed list outside yum/repo
author: svennd

date: 2016-02-10T07:00:15+00:00
url: /find-installed-list-outside-yumrepo/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
If you installed some packages outside yum, not from repo's, finding it using yum won't yield much results.

<pre># yum search lin_*
No matches found</pre>

I know its installed just not where ... Getting a list with installed rpm's (from yum and not-yum) :

<pre>rpm -qa</pre>

now just to filter out what I need :

<pre>rpm -qa |grep lin_*</pre>

results :

<pre>[root@server ~]# rpm -qa | grep lin_*
selinux-policy-3.13.1-60.el7.noarch
libselinux-utils-2.2.2-6.el7.x86_64
selinux-policy-targeted-3.13.1-60.el7.noarch
libpipeline-1.2.3-3.el7.x86_64
libselinux-2.2.2-6.el7.x86_64
readline-6.2-9.el7.x86_64
libnfnetlink-1.0.1-4.el7.x86_64
linux-firmware-20150904-43.git6ebf5d5.el7.noarch
lin_tape-3.0.1-1.x86_64
hardlink-1.0-19.el7.x86_64
util-linux-2.23.2-26.el7.x86_64
libselinux-python-2.2.2-6.el7.x86_64</pre>

I was searching for lin\_tape-3.0.1-1.x86\_64; (tape related stuff) Getting more info : rpm -qi

<pre>[root@mocca ~]# rpm -qi lin_tape
Name        : lin_tape
Version     : 3.0.1
Release     : 1
Architecture: x86_64
Install Date: Fri 23 Oct 2015 03:54:27 PM CEST
Group       : System Environment/Kernel
Size        : 3226031
License     : GPL
Signature   : (none)
Source RPM  : lin_tape-3.0.1-1.src.rpm
Build Date  : Fri 23 Oct 2015 03:54:06 PM CEST
Packager    : IBM Tape SCSI Device Driver Development
Vendor      : IBM
Summary     : IBM Tape SCSI Device Driver for Linux
Description :
The IBM Tape Device Driver, lin_tape, product is a device driver
that provides attachment for the IBM TotalStorage and System
Storage tape devices to Linux compatible platforms.</pre>

I could also check [what is in the package][1], but that I already explained. (rpm -qlp lin_taped.rpm)

Always works : rpm man

Another mystery solved.

 [1]: https://www.svennd.be/what-files-are-in-a-rpm-package/