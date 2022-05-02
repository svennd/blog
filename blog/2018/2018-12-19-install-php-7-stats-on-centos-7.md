---
title: Install PHP 7 Stats on Centos 7
author: svennd

date: 2018-12-19T09:23:53+00:00
url: /install-php-7-stats-on-centos-7/
thumbnail: /img/2020/05/zac-wolff-9ZCo9G4K2Z4-unsplash-scaled.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - php

---
I installed PHP 7.1 through [webtatic][1], except with Apache instead of Nginx, so not the -fpm.  Installing PHP stats package is easy, if you know how, so follow along.

First we need to provide our system with a compiler, and the pear package which contains _pecl_ (yum whatprovides pecl)

<pre>yum install gcc php71w-pear</pre>

After that you can run pecl to install it :

<pre>pecl install stats</pre>

This will however fail, and tell you you need a PHP 5.* version ... however if you specify the latest version it wil just install fine :

<pre>pecl install stats-2.0.3</pre>

And bam, installed. Now we need to activate it in the PHP config :  (new file, or else /etc/php.ini)

<pre>nano /etc/php.d/stats.ini</pre>

add :

<pre>extension=stats.so</pre>

After that, just restart apache and you are good to go :

<pre>systemctl restart httpd</pre>

&nbsp;

&nbsp;

 [1]: https://www.svennd.be/nginx-php-fpm-centos-7/
