---
title: Nginx, stable is not so mainline.
author: svennd

date: 2016-02-15T14:24:20+00:00
url: /nginx-stable-is-not-so-mainline/
thumbnail: /img/2020/05/moritz-kindler-pPxUuFZ4arQ-unsplash-scaled.jpg
categories:
  - Linux
  - SysAdmin

---
Wut ? Yeah, its a shout to all people using Nginx, stable repo is like Centos stable. Meaning no new features, only **major** bug-fixes.  This isn't exactly new information, but its new for people who where living under a _nginx'_ rock like myself.  This means you are missing out on possible allot of nice features and smaller bug-fixes that are considered "breaking". Its all explained pretty good [here on the release][1], but who reads a release announcement from 2014 ...

<!--more-->

  ![Its so clear, why did I miss it during installation?](/img//2015/07/25451072-1.png)
  
So if you like me have this in /etc/yum.repo.d/nginx.repo

<pre>[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/centos/6/$basearch/
gpgcheck=0
enabled=1
</pre>

You can get more free goodies by changing to

<pre>[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/mainline/centos/6/$basearch/
#baseurl=https://nginx.org/packages/centos/6/$basearch/
gpgcheck=0
enabled=1</pre>

Just for comparison :

  * Centos repo's has no version of Nginx
  * Epel release has version 1.0.15
  * Nginx stable has version 1.8.1-1
  * Nginx mainline has version 1.9.11

This means only the mainline at this point can support [http/2][2]!

 [1]: https://www.nginx.com/blog/nginx-1-6-1-7-released/
 [2]: https://www.svennd.be/http2-for-everyone/
