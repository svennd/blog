---
title: mod_rewrite and userdir for Apache2 on chromebook
author: svennd

date: 2015-08-06T06:56:33+00:00
url: /mod_rewrite-and-userdir-for-apache2-on-chromebook/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - ChromeOS
  - Linux
  - SysAdmin
tags:
  - apache

---
I wanted to get mod\_rewrite to work, to get a nice looking url, even tho it is only development version here on my chromebook. (using crouton) While mod\_rewrite is [easy enough,](http://www.svennd.be/invalid-command-rewriteengine/" target="_blank) getting it to work on the mod_userdir was a bit more tricky.

Getting mod_rewrite activated is easy :

<pre class="EnlighterJSRAW" data-enlighter-language="shell"># activate the module
sudo a2enmod rewrite

# edit your sites (or in my case the default)
sudo nano /etc/apache2/sites-enabled/000-default

# search and set AllowOverride from none to all
AllowOverride All

# restart the server
sudo service apache2 restart</pre>

Sadly this will break most copy-paste mod_rewrite rules, also this one :

<pre class="EnlighterJSRAW" data-enlighter-language="shell">RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule .* index.php/$0 [PT,L]</pre>

This won't work, cause the "variable" RewriteBase is not given and the default / will send you to the /var/www (default) where nothing is, resulting in a 404 (unless something is there, -unlikely-)  Fixing it, is a simple as setting your base path. Note that for me, the files where not in the root of my home directory ( /home/svenn/Downloads/my\_project\_dir/)  for those setting see  [this post](http://www.svennd.be/lamp-crouton-chromeos/" target="_blank).

&nbsp;

<pre>RewriteEngine on
RewriteBase /~svenn/my_project_dir/
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule .* index.php/$0 [PT,L]
</pre>