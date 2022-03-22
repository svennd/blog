---
title: PHP 5.4, APC/Zend Optimizer+ on Centos 7
author: svennd

date: 2015-12-31T16:07:56+00:00
url: /php-5-4-apczend-optimizer-on-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
The interwebs is a bit unclear on this topic, but on a "clean" Centos 7.1 (tested on digitalocean) the PHP version is v5.4 , which is a bit of a bugger, since from v5.5 [Zend Optimizer+][1] is included in the core of PHP, making PHP allot faster, about 70% faster for sure, [if we can believe the benchmarks][2]. I need APC not for the user cache but for the in memory storage of compiled PHP bytecode. So both Zend Optimizer+ or APC would do fine. Since Zend Optimizer is included in the PHP core of newer versions, Centos will someday push this (in the not so near future I guess). So I would recommend to use Zend Optimizer+, but feel free to ignore that advice, I am no expert. I will test both and decide then. Thought I doubt I will find much difference.

**APC**

If you need the user cache : <code class="EnlighterJSRAW" data-enlighter-language="null">yum install php-pecl-apcu</code>

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># Install APC
pecl install apc

#nano /etc/php.d/apc.ini
extension=apc.so
apc.enabled=1
</pre>

restart httpd / php-fpm

You can validate that its running + stats by activating the apc.php file. Which can be found [here (source)][3].

**Zend Optimizer+**

<pre>yum install php-pecl-zendopcache</pre>

restart httpd / php-fpm

The config file is located : <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/php.d/opcache.ini</code>

There is no "official" statics script for Zend Opcache I could find, but a [user (Rasmus Lerdorf) has made something similar][4].

<code class="EnlighterJSRAW" data-enlighter-language="null">wget https://raw.githubusercontent.com/rlerdorf/opcache-status/master/opcache.php</code> it in some public directory to see the nice stats.

**FUBAR PHP v5.4**

You can also ditch PHP v5.4 from Centos repo's and add another repo, such as [webtastic repo][5] or [remirepo][6].

 [1]: https://wiki.php.net/rfc/optimizerplus
 [2]: https://docs.google.com/spreadsheets/d/1SywM4I91Cf4gyRniPlc7LML7Tc5ikZjzEjYUt04z_mY/edit#gid=0
 [3]: http://git.php.net/?p=pecl/caching/apc.git;a=blob_plain;f=apc.php;hb=HEAD
 [4]: https://github.com/rlerdorf/opcache-status
 [5]: https://webtatic.com
 [6]: http://blog.remirepo.net/