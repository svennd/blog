---
title: 'crond: (root) BAD FILE MODE'
author: svennd

date: 2015-11-27T14:02:59+00:00
url: /crond-root-bad-file-mode/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
cron is not working. You would see this error in <code class="EnlighterJSRAW" data-enlighter-language="null">/var/log/cron-*</code> or <code class="EnlighterJSRAW" data-enlighter-language="null">/var/log/cron</code> or in some configurations <code class="EnlighterJSRAW" data-enlighter-language="null">/var/log/messages</code>. Its panic time!

Just kidding, bad file mode is just permissions.  You fix it with :

<pre>chmod 0644 /etc/cron.d/your.cron</pre>

Also a common one is WRONG FILE OWNER again rather straight forward :

<pre>chown root.root /etc/cron.d/your.cron</pre>

Note that you need root to be the owner, even tho you can execute it as a non root user.

&nbsp;

&nbsp;