---
title: 'NFS server : increase worker threads'
author: svennd

date: 2017-08-04T14:55:22+00:00
url: /nfs-server-increase-worker-threads/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - nfs

---
I have some NFS servers (_read: the main function is to store data and share it over NFS_) to maintain and mostly they simply work (like everything in Linux). Most variables have been battle tested for ... well forever. So rarely do you need to check on the best trade-off. Well enter the NFS thread counter.

<!--more-->

By vendor default, NFS will start with 8 threads. You can verify this by checking the running process or the statics in /proc.

You can check running processes (note that grep and the mother process is also there)

<pre># ps aux | grep nfs | wc -l
11
</pre>

Alternatively you can check using the proc value's; the value behind th (for threads) is the running threads, the other value's have been deprecated a while ago and are never populated.

<pre># cat /proc/net/rpc/nfsd | grep th
th 8 0 0.000 0.000 0.000 0.000 0.000 0.000 0.000 0.000 0.000 0.000
</pre>

Changing the value is simple, only the there is a trade-off, the threads will use some resources but this is in any current server trivial. The issue is that when there is only 1 request (RPC call) the other threads will try to read in advance, and hence all start accessing the disk. However the default 8 is to low, and will quickly become a bottleneck at heavy loads.  It's therefor advised to increase this number. On a storage backend for a 8 head cluster I increased it to 128, this was the point where the workload did not go faster anymore.  (based on rudimentary checking of _iostat_)

To change it edit (Centos 6.x , 7.x) : <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/sysconfig/nfs</code>

<pre># RPCNFSDCOUNT=8</pre>

to (for example)

<pre># increased threadcount 04/08/2017 - should increase performance
RPCNFSDCOUNT=32</pre>

and restart NFS. _(note : a reload won't work)_

<pre>service nfs-server restart</pre>

That's it !

&nbsp;

&nbsp;