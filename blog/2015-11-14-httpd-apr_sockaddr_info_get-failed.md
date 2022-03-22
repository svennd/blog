---
title: 'httpd: apr_sockaddr_info_get() failed'
author: svennd

date: 2015-11-14T20:06:53+00:00
url: /httpd-apr_sockaddr_info_get-failed/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
A little annoying problem, not blocking but easy to solve!

<pre class="EnlighterJSRAW" data-enlighter-language="shell">[root@host~]# service httpd configtest
httpd: apr_sockaddr_info_get() failed for host.ext
httpd: Could not reliably determine the server's fully qualified domain name, using 127.0.0.1 for ServerName
Syntax OK
</pre>

We could simply add it in /etc/hosts

<pre># fix apache warning
127.0.0.1 host.ext</pre>