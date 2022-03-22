---
title: Installing mariadb on Centos 6.7 32bit
author: svennd

date: 2016-01-07T08:50:43+00:00
url: /installing-mariadb-on-centos-6-7-32bit/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
With Centos 7.1 [MySQL got replaced by MariaDB.][1]  That is great, but now I also want MariaDB on Centos 6.7, [because][2]. By default its not in the repo's (no suprise there) so you need to add the repo yourself. [Good thing MariaDB has a download page telling you howto.][3]

create <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/yum.repos.d/MariaDB.repo</code>

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># MariaDB 10.1 CentOS repository list - created 2016-01-07 08:22 UTC
# http://mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/centos6-x86
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1</pre>

After you get that code, it will tell you detailed info can be found [here][4] if you're google query came on that page first, and like me you did not read the page but just copy/pasted the repo. It won't work 😀 <code class="EnlighterJSRAW" data-enlighter-language="null">yum clean all</code> will save you.

After that you can just run :

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">yum install MariaDB-server MariaDB-client</pre>

Also

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">yum install MySQL-server MySQL-client</pre>

Will resolve in MariaDB.

Have fun with Maria ... db.

 [1]: https://www.svennd.be/mariadb-on-centos-7/
 [2]: https://seravo.fi/2015/10-reasons-to-migrate-to-mariadb-if-still-using-mysql
 [3]: https://downloads.mariadb.org/mariadb/repositories/
 [4]: https://mariadb.com/kb/en/mariadb/yum/