---
title: Installing LibreNMS on Centos 7
author: svennd

date: 2015-11-28T11:28:52+00:00
url: /installing-librenms-on-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
This is a simple manual to install [LibreNMS](http://www.librenms.org" target="_blank), a network monitoring system, on a clean Centos 7.1 . I'm a huge fan of this so I think its important to expand information on this. [Recently](https://github.com/librenms/librenms/pull/2471" target="_blank) I have have tried to update the docs of Centos/RHEL, I'm not sure if they will be accepted as I'm rather new to this project and while I want to give back, I'm not that good with giving neutral documentation 🙂 Below I wrote a quick start, some options I left out, just cause you wane be monitoring rather sooner then later. For more support you could always look in the [LibreNMS docs][1].

**Installation**

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># installation epel repo
yum install epel-release

# install dependencies
yum install php php-cli php-gd php-mysql php-snmp php-pear php-curl httpd net-snmp graphviz graphviz-php mariadb jwhois nmap mtr rrdtool MySQL-python net-snmp-utils php-mcrypt fping git mariadb-server

# some extra stuff
pear install Net_IPv4-1.3.4
pear install Net_IPv6-1.2.2b2</pre>

After installing the packages needed for LibreNMS Its time to setup the database.

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># set to start at boot
chkconfig mariadb on

# start the service
service mariadb start

# configure MariaDB
mysql_secure_installation

# go into mysal console
mysql -u root -p

# in the console :
# create database
CREATE DATABASE librenms;

# give permissions to a new user librenms
# note : CHANGE THE PASSWORD
GRANT ALL PRIVILEGES ON librenms.*
TO 'librenms'@'localhost'
IDENTIFIED BY 'password'
;

# flush the permission
FLUSH PRIVILEGES;</pre>

Installing the software is actually just cloning the master git repo and keeping it up to date, this is done with a cron.

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false" data-enlighter-language="shell"># move to /opt directory
cd /opt

# clone the source
git clone https://github.com/librenms/librenms.git librenms

# move to work directory
cd /opt/librenms

# copy the original configuration to work config
cp config.php.default config.php

# fill in the config.php
# most important are the database (db_*), community
# also add : $config['fping'] = "/usr/sbin/fping"; 
# don't ask me why its not there to begin with ... 
nano config.php

# while we are in the right directory lets create some needed
# directories and execute some SQL data
php build-base.php

# add a new user
# change name, pass and email
php adduser.php name pass 10 email

# add new user
# and add it to the apache group
useradd librenms -d /opt/librenms -M -r
usermod -a -G librenms apache

# create directories
mkdir /opt/librenms/rrd 
mkdir /opt/librenms/logs

# chmod them
chmod 775 /opt/librenms/rrd

# change owner
chown -R librenms:librenms /opt/librenms
chown apache:apache /opt/librenms/logs</pre>

After that I suggest we create the cron, good thing its already in the git.

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># copy it from source to cron
cp /opt/librenms/librenms.nonroot.cron /etc/cron.d

# make it readable
chmod 0644 /etc/cron.d/librenms.nonroot.cron

# I set the ownership to root
# though the execution of the crons will not be root,
# hence the name nonroot (which is recommended)
chown root.root /etc/cron.d/librenms.nonroot.cron</pre>

After that we are ready to setup the webinterface, here I use httpd (Apache), but there are other services also possible (nginx, lighthttpd, ...)

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># make sure Apache is running and started
# it normally already is
chkconfig --levels 235 httpd on

# just in case its not yet
service httpd start

# create a new file
touch /etc/httpd/conf.d/librenms.conf

# then add 
# note : change librenms.example.com !
&lt;VirtualHost *:80&gt;
  DocumentRoot /opt/librenms/html/
  ServerName  librenms.example.com
  CustomLog /opt/librenms/logs/access_log combined
  ErrorLog /opt/librenms/logs/error_log
  AllowEncodedSlashes On
  &lt;Directory "/opt/librenms/html/"&gt;
    AllowOverride All
    Options FollowSymLinks MultiViews
    Require all granted
  &lt;/Directory&gt;
&lt;/VirtualHost&gt;

# remove or comment this file :
rm /etc/httpd/conf.d/welcome.conf
# and finally restart httpd
service httpd restart</pre>

After that you probably want to add more _snmpd _servers then only localhost. I used _snmp-scan.php _for that, you need to set the ranges of ip's it can search for in config.php <code class="EnlighterJSRAW" data-enlighter-language="null">$config['nets'][] </code> It is also possible to just add them manually in the web-interface.

Now give it 10-15 minutes to start collecting data and you are ready to login to the web-interface! (set up in Apache setup)

 [1]: http://docs.librenms.org