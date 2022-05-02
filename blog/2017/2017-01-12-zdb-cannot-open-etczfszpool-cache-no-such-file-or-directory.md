---
title: 'zdb cannot open ‘/etc/zfs/zpool.cache’: No such file or directory'
author: svennd

date: 2017-01-12T15:17:32+00:00
url: /zdb-cannot-open-etczfszpool-cache-no-such-file-or-directory/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - zfs

---
Uh-oh, trying out _zdb_ and finding this error, doesn't breath confidence does it ? Here is how you can solve this minor issue :

<pre># zdb
cannot open '/etc/zfs/zpool.cache': No such file or directory</pre>

Just set it as a value to the zpool :

<pre>zpool set cachefile=/etc/zfs/zpool.cache poolname</pre>

getting the pool name can be done using

<pre>zpool get all</pre>

Thanks to [github user, imp][1]!

 [1]: https://github.com/zfsonlinux/zfs/issues/711