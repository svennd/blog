---
title: 'iconv_strlen(): Wrong charset, conversion from 8bit to UCS-4LE is not allowed'
author: svennd

date: 2015-07-15T10:22:18+00:00
url: /iconv_strlen-wrong-charset-conversion-from-8bit-to-ucs-4le-is-not-allowed/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - codeigniter
  - dompdf
  - pdf
  - php

---
While working with [codeigniter ](http://www.codeigniter.com)& [dompdf ](http://dompdf.github.io/) I hit upon this crazy error, while generating a pdf:

<code class="EnlighterJSRAW" data-enlighter-language="null">iconv_strlen(): Wrong charset, conversion from 8bit to UCS-4LE is not allowed</code>

While this looks like a PHP error, it seems its actually missing some [library](http://stackoverflow.com/questions/22492776/debug-iconv-strlen-error-php-5-5).  Since I work on a Centos 6.5 machine and still wanted a new version of PHP I used [webtastic](https://webtatic.com/). That is a precompiled yum repo, makes live allot easier. So just install this bad boy, once webtastic is installed :

<code class="EnlighterJSRAW" data-enlighter-language="null">yum install php55w-mbstring</code>

Restart your _httpd_ service and voila, problem solved!

Based on this [post](http://serverfault.com/questions/455388/how-to-install-php-xml-and-php-mbstring-on-php-5-4-9-4) I believe in _debian_ php-mbstring is installed per default;

&nbsp;

&nbsp;
