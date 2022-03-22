---
title: 'crond: (root) BAD FILE MODE'
author: svennd

date: 2017-05-10T19:09:39+00:00
url: /crond-root-bad-file-mode-2/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - cron

---
I found these errors today in my the cron log : <code class="EnlighterJSRAW" data-enlighter-language="null">/var/log/cron</code>

<pre> May 10 09:53:01 sysadmin CROND[2267]: (librenms) CMD ( /opt/librenms/alerts.php &gt;&gt; /dev/null 2&gt;&1) May 10 09:54:01 sysadmin crond[63]: (root) BAD FILE MODE (/etc/cron.d/librenms.cron)</pre>

I wrongly assumed a 755 permission was needed for crons, seems I was wrong. For cron execution we only need 644. Surprisingly the issue gets resolved then and cron does execute the cron. I believe this is new behavior in from Centos 7.

Chmod to the solution :

<pre>chmod 0644 /etc/cron.d/librenms.cron</pre>