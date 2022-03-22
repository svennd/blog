---
title: 'YumRepo Error: All mirror URLs are not using ftp, http[s] or file.'
author: svennd

date: 2020-12-07T09:29:29+00:00
url: /yumrepo-error-all-mirror-urls-are-not-using-ftp-https-or-file/
thumbnail: /img/2020/04/centos.jpg
categories:
  - SysAdmin
tags:
  - centos
  - centos 6

---
Greeted with this error while trying to upgrade a Centos 6 machine?

<pre>[root@server root]# yum update
Loaded plugins: fastestmirror
Setting up Update Process
Determining fastest mirrors
YumRepo Error: All mirror URLs are not using ftp, http[s] or file.
 Eg. Invalid release/repo/arch combination/
removing mirrorlist with no valid mirrors: /var/cache/yum/x86_64/6/base/mirrorlist.txt
Error: Cannot find a valid baseurl for repo: base
</pre>

Well bad news [Centos 6][1] is eol 🙁 So there will be no new updates or fixes and you should move over to Centos 7 or even better Centos 8. If you need to use yum however you can fix this :

change from mirror to vault (or mirrorlist to vault)

<pre>nano /etc/yum.repos.d/CentOS-Base.repo</pre>

From :

<pre>[base]
name=CentOS-$releasever - Basevault
baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
</pre>

To : 

<pre>[base]
name=CentOS-$releasever - Basevault
baseurl=http://vault.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
</pre>

Do this for all repo's: base, updates, extras, centosplus and contrib; and you should be able to yum a bit longer 🙂

 [1]: https://wiki.centos.org/About/Product