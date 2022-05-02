---
title: mariadb on centos 7
author: svennd

date: 2015-10-23T14:34:12+00:00
url: /mariadb-on-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I'm just not very happy with Centos 7, things change and I can't handle change. Then again, most changes probably have a good reason, and its all good fun for the brain. Keeping us all awake. Still i'd like to know why eth[0-9]\* was a bad name ? Why do we need enp8s0f[0-9]\* ?  Well anyways, another change -probably for the better- is that MySQL has been swapped with MariaDB, to be honest I have been living under a rock for the last few years, so I had no idea why or what MariaDB was.

Good thing the folks at [digitalocean][1] know what's going on :

> MariaDB is an open source fork of MySQL developed and worked on by the original MySQL developers, lead by Michael “Monty” Widenius. It was created and embraced by the open source community as an effective alternative to MySQL. Although MySQL is still an open source project, it is owned by Oracle, purveyors of their own enterprise software. Worries about the progress of MySQL as well as the status of MySQL as an open source project have prompted the migration to MariaDB. A bug snafu that removed the GPL license from MySQL’s man page caused additional consternation in the open source community as it seemed to restrict the replication of the man pages. Oracle quickly reported this issue as a bug and corrected the copyright notice. Nonetheless, this event was another reminder of how quickly corporate policies could affect the MySQL community.
> 
> When summarizing Wikipedia’s move to MariaDB, the announcer Asher Feldman, included a hint as to why he switched: “…as supporters of the free culture movement, the Wikimedia Foundation strongly prefers free software projects; that includes a preference for projects without bifurcated code bases between differently licensed free and enterprise editions.” Oracle’s different treatment of the enterprise and community versions is another factor that caused a stir. At this point, the future of MySQL, subject to decisions of Oracle, remains murky.

So generally its the latest open-source MySQL, great, lets install it on Centos 7

<pre>yum install mariadb mariadb-server</pre>

Its nothing special, so lets fire it up :

<pre>systemctl start mariadb

# or (not supported)
service mariadb start</pre>

sadly fresh from the repo, this failed 🙁 The error log is located : /var/log/mariadb/mariadb.log. It contained a weird error on a fresh install :

<pre>[ERROR] mysqld: Can't find file: './mysql/plugin.frm' (errno: 13)</pre>

Error 13 is mostly used around permission errors, and in this case it was the same.  <code class="EnlighterJSRAW" data-enlighter-language="null">cat /etc/my.cnf | grep datadir</code> will tell you where the datadir is; this is the directory with most of the files <del>mysql</del> mariadb uses. For me /var/lib/mysql/mysql, /var/lib/mysql/performance_schema and /var/lib/mysql/test where all owned by root. Changing those to mysql ownership fixed this problem 🙂

<pre># give good permissions
chown -R mysql.mysql /var/lib/mysql/mysql
chown -R mysql.mysql /var/lib/mysql/performance_schema
chown -R mysql.mysql /var/lib/mysql/test

# restart it
service mariadb start</pre>

 [1]: https://www.digitalocean.com/community/tutorials/switching-to-mariadb-from-mysql