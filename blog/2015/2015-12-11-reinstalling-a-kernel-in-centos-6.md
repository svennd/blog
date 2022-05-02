---
title: Reinstalling a Kernel in Centos 6
author: svennd

date: 2015-12-11T14:59:54+00:00
url: /reinstalling-a-kernel-in-centos-6/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
While most people would think (including me) that <code class="EnlighterJSRAW" data-enlighter-language="null">yum reinstall kernel kernel-headers kernel-firmware</code> would be enough, its not!  You need to select the one you wanne reinstall by removing it and then reinstalling it. (since its the only package that has multiple versions I guess)

For me this was :

<pre># remove the evil
yum remove kernel-2.6.32-573.8.1.el6.x86_64 kernel-devel-2.6.32-573.8.1.el6.x86_64

# reinstall it again
# note : in my case I was oke with the "latest and newest" kernel
yum install kernel kernel-devel kernel-firmware</pre>

Happy kernel reinstalling ! (don't think that a thing)