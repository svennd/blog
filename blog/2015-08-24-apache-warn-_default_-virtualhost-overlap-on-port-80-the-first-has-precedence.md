---
title: 'Apache: [warn] _default_ VirtualHost overlap on port 80'
author: svennd

date: 2015-08-24T14:38:43+00:00
url: /apache-warn-_default_-virtualhost-overlap-on-port-80-the-first-has-precedence/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
<pre>Apache: [warn] _default_ VirtualHost overlap on port 80</pre>

This happens when you have 2 or more <code class="EnlighterJSRAW" data-enlighter-language="null">&lt;VirtualHost *:80&gt;</code> and did not set <code class="EnlighterJSRAW" data-enlighter-language="null">NameVirtualHost *:80</code>  This one liner will fix it :

<pre>echo "NameVirtualHost *:80" &gt;&gt; /etc/httpd/conf/httpd.conf</pre>

Every time I set up a server I forget about this behavior so its mostly for myself. 🙂