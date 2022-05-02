---
title: 'google history : my “tar” cheat sheet/bible'
author: svennd

date: 2016-05-26T15:30:57+00:00
url: /my-tar-bible/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
Years I have been using the **tar** command now, and still, this xkcd comic holds true :

[![I don't know what's worse--the fact that after 15 years of using tar I still can't keep the flags straight, or that after 15 years of technological advancement I'm still mucking with tar flags that were 15 years old when I started.](https://imgs.xkcd.com/comics/tar.png)][1]

While **tar** is not that complex, my brain refuses to learn these commands :

**Create a archive (gzip compression)**

<pre>tar cvfz archive.tar.gz dir/</pre>

c - create  
v - verbose  
f - archive name  
z - use gzip

**Untar (/extract) an archive  
** 

<pre>tar xvfz archive.tar.gz</pre>

x - extract  
v - verbose  
f - archive name  
z - use gzip

**Create a archive (using lz4 compression)  
** lz4 is super fast and so when size is not really a problem its easy to use lz4. (for example to store short-term backups I tend to use lz4)

<pre>tar cvf - dir/ | lz4 - archive.tar.lz4</pre>

**Untar (/extract) an lz4 archive**

<pre>lz4 -dc --no-sparse archive.tar.lz4 | tar xvf -</pre>

d - decompression  
c - cat to stdout  
x - extract  
v - verbose  
f - file name (in this case - = stdout)

My search history includes way to many variations on "how to use tar" hence this mini cheat sheet.

 [1]: https://xkcd.com/1168/