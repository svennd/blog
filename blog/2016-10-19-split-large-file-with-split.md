---
title: split large file with … split.
author: svennd

date: 2016-10-19T07:20:08+00:00
url: /split-large-file-with-split/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
A small useful hack (/use of) when you need to cut a large (huge) file in pieces to move it to tapes (2.5 Tb), cd (800 Mb), ... . Here comes split !

  ![split](/img/2016/10/ballet-1033167_640-300x198.jpg)

<pre>split -b 2T full_data.tar.gz "data.tar.gz.part"</pre>

This will split the _full_data.tar.gz_ (+6 Tb) in pieces of 2Tb each, each part will be named _data.tar.gz.part*_

* is an option (see _split -help_), but default is double letter (aa, ab, ac, ...., zz)

now, to join them together again, you can join them using _cat_

<pre>cat data.tar.gz* &gt; full_data.tar.gz</pre>

Go forth and split those large files!
