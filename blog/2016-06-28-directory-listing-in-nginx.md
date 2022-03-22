---
title: Directory listing in Nginx
author: svennd

date: 2016-06-28T14:18:59+00:00
url: /directory-listing-in-nginx/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
By default, Nginx doesn't put directory listing on, in a development system that's a really useful feature (many, many projects) so I tend to put it on. <code class="EnlighterJSRAW" data-enlighter-language="null">autoindex on;</code>

In the server block under location :

<pre>server {
    location / {
        autoindex on;
    }
}</pre>

Worked like a charm !

  ![autoindex in action, showing the directory listing](/img//2015/07/25428800-1.png)

&nbsp;
