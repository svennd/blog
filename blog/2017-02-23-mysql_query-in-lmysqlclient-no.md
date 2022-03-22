---
title: mysql_query() in -lmysqlclient… no
author: svennd

date: 2017-02-23T11:09:11+00:00
url: /mysql_query-in-lmysqlclient-no/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin

---
During installing mysql2 gem on ruby :

<pre>checking for mysql_query() in -lmysqlclient... no
checking for main() in -lm... yes
checking for mysql_query() in -lmysqlclient... no
checking for main() in -lz... yes
checking for mysql_query() in -lmysqlclient... no
checking for main() in -lsocket... no
checking for mysql_query() in -lmysqlclient... no
checking for main() in -lnsl... yes
checking for mysql_query() in -lmysqlclient... no
checking for main() in -lmygcc... no
checking for mysql_query() in -lmysqlclient... no
*** extconf.rb failed ***
Could not create Makefile</pre>

Its basically saying you miss a library, good thing someone tracked it down (see [askubuntu][1])

So for Centos 7 : (for Centos 6 most likely mysql-devel)

<pre>yum install mariadb-devel</pre>

for Debian/Ubuntu :

<pre>apt-get install libmysqlclient-dev</pre>

Damn you Ruby.

 [1]: http://askubuntu.com/questions/17856/installing-mysql-ruby-gem