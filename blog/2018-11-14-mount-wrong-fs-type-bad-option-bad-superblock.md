---
title: 'mount: wrong fs type, bad option, bad superblock'
author: svennd

date: 2018-11-14T10:02:51+00:00
url: /mount-wrong-fs-type-bad-option-bad-superblock/
thumbnail: /img/2020/05/edward-howell-OEfm0hrY_rw-unsplash-scaled.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - filesystem

---
On Centos 7.5 base install, there are no utility's installed by default. So when I tried to mount an NFS share I got this error :

<pre>mount: wrong fs type, bad option, bad superblock on svennd.be:/data,
       missing codepage or helper program, or other error
       (for several filesystems (e.g. nfs, cifs) you might
       need a /sbin/mount.&lt;type&gt; helper program)

       In some cases useful info is found in syslog - try
       dmesg | tail or so.</pre>

This clearly tells us it cannot communicate with the NFS share, fixing can be done installing : nfs-utils for Centos.

<pre>yum install nfs-utils</pre>

for Debian variants (Mint, Ubuntu, ...) this would be :

<pre>apt install nfs-common</pre>

After that, I see happy shares 🙂

<pre class="EnlighterJSRAW" data-enlighter-theme="classic" data-enlighter-language="generic">mount.nfs: timeout set for Wed Nov 14 12:57:50 2018
mount.nfs: trying text-based options 'soft,intr,retrans=2,rsize=32768,wsize=32768,nfsvers=3,tcp,addr=svennd.be'
mount.nfs: prog 100003, trying vers=3, prot=6
mount.nfs: trying svennd.be prog 100003 vers 3 prot TCP port 2049
mount.nfs: prog 100005, trying vers=3, prot=6
mount.nfs: trying svennd.be prog 100005 vers 3 prot TCP port 20048
/data    : successfully mounted</pre>

&nbsp;