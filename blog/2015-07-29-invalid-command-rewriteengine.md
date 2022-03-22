---
title: Invalid command ‘RewriteEngine’
author: svennd

date: 2015-07-29T06:16:16+00:00
url: /invalid-command-rewriteengine/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin

---
A simple apache error, this fixes it :

<code class="EnlighterJSRAW" data-enlighter-language="null">sudo a2enmod rewrite</code>

Followed by a restart of your httpd server. (reload might work)

<code class="EnlighterJSRAW" data-enlighter-language="null">service apache2 restart</code>