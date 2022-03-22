---
title: Call to undefined function imagecreate()
author: svennd

date: 2016-02-20T15:49:40+00:00
url: /call-to-undefined-function-imagecreate/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
When PHP poops this out after a move :

<pre>Call to undefined function imagecreate()</pre>

You forgot a dependency! This one is easy however.

<pre>yum install php-gd</pre>

Don't forget :

<pre>service httpd restart

# or systemd
systemctl restart httpd
# and get not even a "ok" back ...</pre>

&nbsp;