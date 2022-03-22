---
title: undefined function ibase_connect()
author: svennd

date: 2015-08-20T10:12:15+00:00
url: /fatal-error-call-to-undefined-function-ibase_connect-phpfatal-error-call-to-undefined-function-ibase_connect/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Ouch, while trying to connect to a [firebird][1] database (I know its not popular)

<pre>Fatal error: Call to undefined function ibase_connect()</pre>

The solution for me was simple, the module was not installed ! Since I work with centos, I have a [alternative repo][2] (as the official one doesn't have newer versions of PHP)

<pre>yum install php55w-interbase
service httpd restart</pre>

Restarting httpd and fixed !

 [1]: http://www.firebirdsql.org/
 [2]: https://webtatic.com/packages/php56/