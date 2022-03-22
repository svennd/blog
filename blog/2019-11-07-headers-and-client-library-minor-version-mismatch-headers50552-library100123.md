---
title: Headers and client library minor version mismatch. Headers:50552 Library:100123
author: svennd

date: 2019-11-07T21:42:53+00:00
url: /headers-and-client-library-minor-version-mismatch-headers50552-library100123/
thumbnail: /img/2020/05/wood.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - mysql

---
I got this weird error;

<pre>Warning: mysql_connect(): Headers and client library minor version mismatch. Headers:50162 Library:50524
Filename: mysqli/mysqli_driver.php</pre>

After checking PHP seems there was a different version of MariaDB installed; 5.5 vs 10.1;

<pre>php -i |grep API
Server API =&gt; Command Line Interface
PHP API =&gt; 20160303
Zend Extension Build =&gt; API320160303,NTS
PHP Extension Build =&gt; API20160303,NTS
MHASH API Version =&gt; Emulated Support
Client API library version =&gt; 10.1.23-MariaDB
Client API header version =&gt; 5.5.52-MariaDB
Client API version =&gt; 10.1.23-MariaDB
Phar API version =&gt; 1.1.1
</pre>

To fix it I installed 'mysql native driver' instead of the MySQL lib what is mysqlnd ? See this [writeup by ulf-wendel.][1]

fix :

<pre>yum remove php71w-mysql
yum install php71w-mysqlnd

[root@greenleader /]# service php-fpm restart
Redirecting to /bin/systemctl restart php-fpm.service
[root@greenleader /]#
</pre>

See also [stackoverflow][2] and [MySQL explanation][3].

 [1]: http://blog.ulf-wendel.de/2007/php-what-is-mysqlnd-do-i-need-it/
 [2]: https://stackoverflow.com/questions/10759334/headers-and-client-library-minor-version-mismatch
 [3]: https://dev.mysql.com/downloads/connector/php-mysqlnd/