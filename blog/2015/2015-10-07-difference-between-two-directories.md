---
title: difference between two directories
author: svennd

date: 2015-10-07T12:06:19+00:00
url: /difference-between-two-directories/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I'm a bit a fan of [observium][1], sadly they switched from "open-source" to a fremium model. The Community version is free, but only gets two updates a year. Its also not visible when a new version is out and the download is "latest" not version/revision .... So you end up gambling when there is an update.  Even within the structure there is no clear version given.

So the only way to know if its a new version is to compare the two directories but guess what, its as easy as :

<pre>diff -rq /path/to/observium /path/to/observium_new?</pre>

Good little tool. By the way it seems I was up-to-date ! (no new eye-candy)

 [1]: http://www.observium.org