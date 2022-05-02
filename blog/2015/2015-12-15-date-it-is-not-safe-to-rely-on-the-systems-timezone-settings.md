---
title: 'date(): It is not safe to rely on the system’s timezone settings.'
author: svennd

date: 2015-12-15T10:08:11+00:00
url: /date-it-is-not-safe-to-rely-on-the-systems-timezone-settings/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
A little annoying : 

<pre>[:error] [pid 8725] [client :51515] PHP Warning:  date(): It is not safe to rely on the system's timezone settings. You are *required* to use the date.timezone setting or the date_default_timezone_set() function. In case you used any of those methods and you are still getting this warning, you most likely misspelled the timezone identifier. We selected the timezone 'UTC' for now, but please set date.timezone to select your timezone. in on line 29, referer:</pre>

PHP being a bit silly is it not ? Fix this crazy error : (Centos 7)

<code class="EnlighterJSRAW" data-enlighter-language="null">nano +878 /etc/php.ini</code>

Change

<code class="EnlighterJSRAW" data-enlighter-language="null">;date.timezone = </code>

to

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">date.timezone = Europe/Brussels</pre>

[possible time zones][1]

restart (reload might work) httpd to get it active

<code class="EnlighterJSRAW" data-enlighter-language="null">service httpd restart</code>

Be gone from my log file evil error!

 [1]: http://php.net/manual/en/timezones.php