---
title: rpc.statd is not running but is required for remote locking. Centos 6
author: svennd

date: 2018-08-09T11:55:55+00:00
url: /rpc-statd-is-not-running-but-is-required-for-remote-locking-centos-6/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - nfs

---
Got this error :

<pre>mount.nfs: rpc.statd is not running but is required for remote locking.
mount.nfs: Either use '-o nolock' to keep locks local, or start statd.
mount.nfs: an incorrect mount option was specified</pre>

You forgot the rpcbind service !

<pre>yum install rpcbind
service rpcbind start
chkconfig rpcbind on</pre>

Glad to help 😉