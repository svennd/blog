---
title: Recursively md5/sha1/sha256/sha512 a directory with files
author: svennd

date: 2016-09-12T13:53:09+00:00
url: /recursively-md5sha1sha256sha512-a-directory-with-files/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin

---
A quick hack for checking content is consistent over systems using a hashing method.  This is for md5, which is fast but some collision is possible if carefully crafted.  Use this on each directory.

<pre>find . -type f -exec md5sum "{}" + &gt; checksum.list</pre>

Then checking if both are equal :

<pre>diff first.list second.list</pre>

No difference : data on both ends same. This also works with sha256sum.

Good luck!