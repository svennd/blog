---
title: 'lbzip2: Cannot exec: No such file or directory'
author: svennd

date: 2021-06-29T09:47:23+00:00
url: /lbzip2-cannot-exec-no-such-file-or-directory/
thumbnail: /img/2020/05/dylan-alcock-R4ZiMTvAQbA-unsplash-scaled.jpg
categories:
  - Linux
  - SysAdmin

---
This is a while ago, a missing library. When trying to decompress bz2 :

<pre># tar -xf words.bz2
tar (child): lbzip2: Cannot exec: No such file or directory
tar (child): Error is not recoverable: exiting now
tar: Child returned status 2
tar: Error is not recoverable: exiting now
</pre>

Something is missing to decompress bz2, that something is bzip2 🙂

<pre>dnf install bzip2
yum install bzip2
</pre>

Now it should decompress 🙂