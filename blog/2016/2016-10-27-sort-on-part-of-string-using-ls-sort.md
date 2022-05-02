---
title: 'Sort on part of string using ls & sort'
author: svennd

date: 2016-10-27T07:55:16+00:00
url: /sort-on-part-of-string-using-ls-sort/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
Sometimes sorting some files seems easy, but its not. For example this part :

_(random\_string)\_(numeric\_id)\_(allot\_of\_digits)-(random_string)_

Of course I would like to sort on numeric_id, and not on the random string; This is where command guru's are master, I'm but a humble user, so I'd like to share my hacky solution !

<pre>ls -l | sort -t_ -k2</pre>

bam, sorted.