---
title: setting up LAMP stack in crouton ChromeOS
author: svennd

date: 2015-07-27T18:35:03+00:00
url: /lamp-crouton-chromeos/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - ChromeOS
  - Linux
  - SysAdmin
tags:
  - chromebook
  - LAMP
  - userdir

---
While Its not particular difficult installing a [lamp stack][1] on Ubuntu/Debian, or [Centos][2]/RHEL; Installing it on ChromeOS/Chromebook is a [bit more tricky][3], definitely when you want access from ChromeOS Downloads, since this is a userdir (/home/user/Downloads/).

I found this, although not very secure, working :

<pre># install LAMP
sudo apt-get install apache2 mysql-server libapache2-mod-auth-mysql php5-mysql php5 libapache2-mod-php5 php5-mcrypt

# deploy patch 
# add to /etc/rc.local before exit 0;
/etc/init.d/apache2 start

export HOME=/etc/mysql
umask 007
[ -d /var/run/mysqld ] || install -m 755 -o mysql -g root -d /var/run/mysqld
/usr/sbin/mysqld &

# modify /etc/apache2/mods-available/userdir.conf
&lt;IfModule mod_userdir.c&gt;
 UserDir Downloads
 UserDir disabled root

 &lt;Directory /home/*/Downloads&gt;
 #AllowOverride FileInfo AuthConfig Limit Indexes
 AllowOverride All
 #Options MultiViews Indexes SymLinksIfOwnerMatch IncludesNoExec
 Options MultiViews Indexes SymLinksIfOwnerMatch
 &lt;Limit GET POST OPTIONS&gt;
 Order allow,deny
 Allow from all
 &lt;/Limit&gt;
 &lt;LimitExcept GET POST OPTIONS&gt;
 Order deny,allow
 Deny from all
 &lt;/LimitExcept&gt;
 &lt;/Directory&gt;
&lt;/IfModule&gt;
# activate userdir mod
sudo a2enmod userdir

# restart the server
sudo service apache2 restart

# chmod is NEEDED (adapt your name)
chmod 755 /home/svenn/Downloads

# my working directory
chmod -R 755 /home/svenn/Downloads/CodeIgniter/</pre>

Now you can access your localhost : http://localhost/~user/

 [1]: https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu
 [2]: https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-centos-6
 [3]: https://github.com/dnschneid/crouton/wiki/Running-servers-in-crouton#lamp