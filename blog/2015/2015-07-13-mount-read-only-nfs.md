---
title: mount read only nfs
author: svennd

date: 2015-07-13T08:46:54+00:00
url: /mount-read-only-nfs/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
tags:
  - mount
  - nfs

---
While most people have problems that the nfs mounts as read-only, I really only want to mount it read-only. So here's the command.

<code class="EnlighterJSRAW" data-enlighter-language="null">mount -o ro source:/data/ local/directory/</code>

&nbsp;

&nbsp;