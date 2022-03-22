---
title: Install ZFS on Centos
author: svennd

date: 2017-02-17T14:26:30+00:00
url: /install-zfs-on-centos/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - zfs

---
![](/img/2017/02/openzfs-trans.png)Installing ZFS on Centos has been ironed out pretty much, so just follow along. There are three ways I know of to install ZFS on Centos. The two methods are recommended, as they use a repository, the last is just compiling from source, which I like, since I decide when its updated. (as kernel updates have caused some issue's after reboots for ZFS)

The ZOL (ZFS on Linux) advice is to use kABI-tracking kmod, so that on updating to a newer kernel, ZFS does not need to be rebuild, however the default repository points to DKMS style tracking. This does work, but I had issue's in the past,  so I chose to compile from source and update when I know we can handle some downtime.

<!--more-->

Note : this guide assumes you installed epel repository if not, <code class="EnlighterJSRAW" data-enlighter-language="null">yum install epel-release</code>

### Install ZFS Repo

If you want DKMS or kABI-tracking kmod, we need to install the ZFS repository; Based on the Centos version (or the RHEL version) you need to pick a package; _Note that for **Centos 7.3 **you need a special version due to changes in the kABI kernel part from 3.10.0-514. (if you want to take kABI)_

  * For Centos 7.3 : <code class="EnlighterJSRAW" data-enlighter-language="null">yum install http://download.zfsonlinux.org/epel/zfs-release.el7_3.noarch.rpm</code>
  * For Centos 7 : <code class="EnlighterJSRAW" data-enlighter-language="null">yum install http://download.zfsonlinux.org/epel/zfs-release.el7.noarch.rpm</code>
  * For Centos 6 : <code class="EnlighterJSRAW" data-enlighter-language="null">yum install http://download.zfsonlinux.org/epel/zfs-release.el6.noarch.rpm</code>

After this, it's good practice (but rarely done) to validate the fingerprint, this should be done using gpg, the output should be exactly as below.

<pre>gpg --quiet --with-fingerprint /etc/pki/rpm-gpg/RPM-GPG-KEY-zfsonlinux
pub  2048R/F14AB620 2013-03-21 ZFS on Linux &lt;zfs@zfsonlinux.org&gt;
      Key fingerprint = C93A FFFD 9F3F 7B03 C310  CEB6 A9D5 A1C0 F14A B620
sub  2048R/99685629 2013-03-21</pre>

### Installing ZFS using DKMS

_note : you need to install ZFS Repo first_

Installing using DKMS is easy once you installed the repository, however you do need kernel development library's for each new kernel, this can be installed using kernel-devel; In one nice package :

<pre>yum install kernel-devel zfs</pre>

And you are good to go.

### Installing ZFS using the kABI-tracking kmod

_note : you need to install ZFS Repo first_

Since you chose the kABI method, we need to enable the correct repo's this can be done in the <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/yum.repos.d/zfs.repo</code> file. You should edit line 4 and 12, put the first enabled to 0 and the second under _[zfs-kmod]_ to 1; It should look like : (I ignored the _zfs-source_ and _zfs-testing_)

<pre>[zfs]
name=ZFS on Linux for EL7 - dkms
baseurl=http://download.zfsonlinux.org/epel/7.3/$basearch/
enabled=0
metadata_expire=7d
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-zfsonlinux

[zfs-kmod]
name=ZFS on Linux for EL7 - kmod
baseurl=http://download.zfsonlinux.org/epel/7.3/kmod/$basearch/
enabled=1
metadata_expire=7d
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-zfsonlinux</pre>

After that you can simply run :

<pre>yum install zfs</pre>

And you are ready to go!

### Installing ZFS by compiling from source

This for sure it the least convenient way, but this way we know exactly when ZFS is updating and when a downtime is accepted. I tend to use _yum-cron_ but this method ZFS does not get updated. (I also could blacklist ZFS from updating but I prefer this method) Building from source can be done straight from the ZFS git repository, but I prefer to download the releases only. That's the way I'm using here.

First download the latest release : (both spl and zfs are required)

<pre>wget https://github.com/zfsonlinux/zfs/releases/download/zfs-0.6.5.9/spl-0.6.5.9.tar.gz
wget https://github.com/zfsonlinux/zfs/releases/download/zfs-0.6.5.9/zfs-0.6.5.9.tar.gz</pre>

untar them :

<pre>tar xvzf spl-0.6.5.9.tar.gz
tar xvzf zfs-0.6.5.9.tar.gz</pre>

We also need some packages to have the compiling run smooth :

<pre>yum install libtool automake autoconf kernel-devel zlib-devel libuuid-devel libattr-devel libblkid-devel libselinux-devel libudev-devel device-mapper-devel</pre>

Next we start by compiling SPL, this is the [shim][1] layer module that translates the API requests, so that ZOL (ZFS on Linux) is compatible with OpenZFS and hence can be used across multiple platforms.

<pre>cd spl-0.6.5.9
sh autogen.sh
./configure
make -s -j$(nproc)</pre>

Then we move on to the real deal, ZFS. The code is based on the upstream OpenZFS code, adapted to Linux. Compiling is similar to the spl code; only you have to give the location where the SPL code was compiled; for me that is <code class="EnlighterJSRAW" data-enlighter-language="null">/opt/zfs/zfs_0.6.5.9/spl-0.6.5.9/</code> if you forgot the configure will tell you nicely :

<pre>checking spl build directory... Not found
configure: error:
        *** Please make sure the kmod spl devel &lt;kernel&gt; package for your
        *** distribution is installed then try again.  If that fails you
        *** can specify the location of the spl objects with the
        *** '--with-spl-obj=PATH' option.</pre>

So run :

<pre>cd zfs-0.6.5.9
sh autogen.sh
./configure --with-spl=/opt/zfs/zfs_0.6.5.9/spl-0.6.5.9/
make -s -j$(nproc)
make install</pre>

If its an upgrade (like mine) ZFS module is already loaded, if its a new install <code class="EnlighterJSRAW" data-enlighter-language="null">modprobe zfs</code> should load the module and you are good to go :

<pre>zfs list
zpool list</pre>

&nbsp;

Based on the excellent [RHEL install guide][2] and [building ZFS from source][3].

Changes :

  * Kabi method tested for fresh & clean Centos 7.3 (08/03/2017)

 [1]: https://en.wikipedia.org/wiki/Shim_(computing)
 [2]: https://github.com/zfsonlinux/zfs/wiki/RHEL-%26-CentOS
 [3]: https://github.com/zfsonlinux/zfs/wiki/Building-ZFS