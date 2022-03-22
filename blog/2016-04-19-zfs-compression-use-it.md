---
title: 'zfs compression : use it!'
author: svennd

date: 2016-04-19T13:43:21+00:00
url: /zfs-compression-use-it/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - zfs

---
I'm playing with ZFS for a while now and I read that compression should be turned on, on pretty much anything that has a decent CPU. Most of our data is already compressed, so our idea was, why take the speed hit for no real advantage ... A second machine was bought, and I decided to ignore my colleague's and try to copy our badly to compress data and see what ratio's are...

You can see compression using :

<pre># zfs get all | grep compr
huginn                 compressratio         1.33x                   -
huginn                 compression           lz4                     local
huginn/stuff           compressratio         1.21x                   -
huginn/stuff           compression           lz4                     inherited from huginn
huginn/statstuff       compressratio         1.35x                   -
huginn/statstuff       compression           lz4                     inherited from huginn</pre>

21-30% compression ... for uncompressable data that is pretty nice ! So without delay : activate compression like this :

<pre>zfs set compression=lz4 dataset</pre>

Jeej for zfs!