---
title: Recursively make md5sum
author: svennd

date: 2015-08-27T09:20:57+00:00
url: /recursively-make-md5sum/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I wanted to get md5sum of all directory and sub-directory's. The default command sadly doesn't support recursion. Good thing, [Michael Simons][1] fixed it for me :

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">find ./ -type f -print0 | xargs -0 md5sum &gt; /checksums.md5</pre>

Works just nicely on Centos6.5 ! Great little hack.

 [1]: http://info.michael-simons.eu/2008/10/25/recursively-md5sum-all-files-in-a-directory-tree/