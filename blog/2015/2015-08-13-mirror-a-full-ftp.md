---
title: mirror a full ftp
author: svennd

date: 2015-08-13T12:34:25+00:00
url: /mirror-a-full-ftp/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
Downloading a full ftp, with tons of small files can be allot of click work, so generally users ship it of to IT/BioIT  . When downloading from a ftp, you cannot use rsync -sadly-. As such, we need other solutions, lucky -for me- wget just is the tool we need.

<pre>wget -m --ask-password ftp://user@IP_ADDDR:/dir/to/download</pre>

Starting this in a _GNU _[screen][1] and voila time for :

<img loading="lazy" class="alignnone wp-image-1004 size-full" src="/img//2015/07/29695664-1.png" width="413" height="360" srcset="/img/2015/07/29695664-1.png 413w, /img/2015/07/29695664-1-300x262.png 300w, /img/2015/07/29695664-1-1x1.png 1w" sizes="(max-width: 413px) 100vw, 413px" /> 

[source][2]

&nbsp;

 [1]: http://www.svennd.be/linux-screen/
 [2]: https://xkcd.com/303/