---
title: PID file /run/nginx.pid not readable (yet?) after start
author: svennd

date: 2016-06-27T12:45:16+00:00
url: /pid-file-runnginx-pid-not-readable-yet-after-start/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Aauch, after installing Nginx on a new machine Centos 7, I could not start it... for some weird error :

<pre>systemd: Starting nginx - high performance web server...
nginx: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: nginx: configuration file /etc/nginx/nginx.conf test is successful
systemd: PID file /run/nginx.pid not readable (yet?) after start.
systemd: nginx.service start operation timed out. Terminating.
systemd: Failed to start nginx - high performance web server.
systemd: Unit nginx.service entered failed state.
systemd: nginx.service failed.
</pre>

The file did exist at /var/run/nginx.pid and was readable. The fix after some searching :

change <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/nginx/nginx.conf</code> :

to

<pre>pid        /var/run/nginx.pid;</pre>

from

<pre>pid        /run/nginx.pid;</pre>

and restart the service, that's it ? yep, that's it!