---
title: 'Fatal error: Call to undefined function mb_strlen()'
author: svennd

date: 2017-01-28T12:32:12+00:00
url: /fatal-error-call-to-undefined-function-mb_strlen/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
A common not found during trial run of some software 🙂

<pre>Fatal error: Call to undefined function mb_strlen() in file.php on line 123</pre>

Missing dependency :

on Debian, Ubuntu and other friends :

<pre>apt-get install php7.0-mbstring</pre>

on Centos, RHEL and other friends :

<pre>yum install php-mbstring</pre>

Or in my case PHP 7 using the [webtatic.com][1] repo

<pre>yum install php70w-mbstring</pre>

&nbsp;

 [1]: https://webtatic.com