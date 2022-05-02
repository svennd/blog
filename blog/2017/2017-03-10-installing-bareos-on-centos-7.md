---
title: Installing bareos on Centos 7
author: svennd

date: 2017-03-10T10:35:18+00:00
url: /installing-bareos-on-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Bareos (Backup Archiving Recovery Open Sourced) is a backup solution, its a fork of the better known Bacula. At work we use a commercial package called [retrospect][1], it has proven itself in many situations, never the less, its good to look out what free and open-source alternatives are out there. Bareos caught my eye in particular, cause they seem very active in the FOSS community ([see fosdem 2017][2]), always a good sign.

Bareos has a huge [documentation][3] * , sadly not everything is up-to-date or easy to understand for beginners like myself. The 500+ page manual is also not something you browse through quickly. That's why I will post my guide(s) here, not cause the documentation is incomplete or perhaps not up-to-date. But to fellow sysadmin's looking for a quick run at bareos without having to read through all the documentation. Like any open-source project, the more people who are involved the better the chance a project can survive on the long term.

* Since publication, improvements have been made to the [documentation][4];

<!--more-->

Installation on Centos 7 is a blast. However due to the many moving parts, its good to keep your head in the game.

First start with adding the epel repository :

<pre>yum install epel-release
yum install wget nano</pre>

Follow by adding the official bareos repo : (you could also copy-paste [this link][5])

<pre>wget -O /etc/yum.repos.d/bareos.repo http://download.bareos.org/bareos/release/latest/CentOS_7/bareos.repo</pre>

You are now ready to install the packages, I chose for mariadb as I am most familiar with it :

<pre>yum install bareos bareos-database-mysql bareos-webui mariadb-server</pre>

Next we need to setup mariadb and setup auto-login for root.  Let's start mariadb, and configure it to start at boot :

<pre>systemctl enable mariadb
systemctl start mariadb</pre>

next run the _mysql\_secure\_install_ script provided :

<pre>mysql_secure_installation</pre>

next create a file :

<pre>nano ~/.my.cnf</pre>

with content : (change _PASSWORD_)

<pre>[client]
host=localhost
user=root
password=PASSWORD</pre>

Then we can install bareos schema using the provided MySQL scripts :

<pre>/usr/lib/bareos/scripts/create_bareos_database
/usr/lib/bareos/scripts/make_bareos_tables
/usr/lib/bareos/scripts/grant_bareos_privileges</pre>

This should result in something like :

<pre>[root@server ~]# /usr/lib/bareos/scripts/create_bareos_database
Creating mysql database
Creating of bareos database succeeded.
[root@server ~]# /usr/lib/bareos/scripts/make_bareos_tables
Making mysql tables
Creation of Bareos MySQL tables succeeded.
[root@server ~]# /usr/lib/bareos/scripts/grant_bareos_privileges
Granting mysql tables
Privileges for user bareos granted ON database bareos.</pre>

Now we are ready to start the services; since we installed the webui its good to start httpd as well, in case it was already running, reload/restart it.

<pre>systemctl start bareos-dir
systemctl start bareos-sd
systemctl start bareos-fd
systemctl start httpd</pre>

The web-bareos-gui should already be available now under http://ip/bareos-webui/ the configuration for that can be found in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/httpd/conf.d/bareos-webui.conf</code>

In my case, I had to let selinux security know that it could connect :

<pre>setsebool -P httpd_can_network_connect on</pre>

The web interface looks like :

![web login of bareos](/img/2017/03/webbased_bareos.png) 

&nbsp;

What login you ask, ah, we have not yet created it so let's go ahead and create a new user. Start the bareos-console :

<pre>bconsole</pre>

add user

<pre>configure add console name=admin password=password123 profile=webui-admin
</pre>

That gives something along the line :

<pre>[root@bareos ~]# bconsole
Connecting to Director localhost:9101
 Encryption: PSK-AES256-CBC-SHA
1000 OK: bareos-dir Version: 18.2.5 (30 January 2019)
bareos.org build binary
bareos.org binaries are UNSUPPORTED by bareos.com.
Get official binaries and vendor support on https://www.bareos.com
You are connected using the default console

Enter a period to cancel a command.
*configure add console name=admin password=PASSWORD profile=webui-admin
Created resource config file "/etc/bareos/bareos-dir.d/console/admin.conf":
Console {
  Name = admin
  Password = PASSWORD
  Profile = webui-admin
}
</pre>

_Note : Can't login ? Try adding this to the config :_

<pre>nano /etc/bareos/bareos-dir.d/console/admin.conf</pre>

<pre>TLS Enable = No</pre>

resulting in a total file :

<pre>Console {
  Name = admin
  Password = PASSWORD
  Profile = webui-admin
  TLS Enable = No
}
</pre>

Restart the director to be sure :

<pre>systemctl restart bareos-dir</pre>

And that's it! After logging in on the web interface you will see something like :

[![](/img/2017/03/bareos-1024x452.png)][6]

&nbsp;

Next we will add our first client and create a job for it to run on regular base.

[Continue reading][7]

 [1]: https://www.retrospect.com
 [2]: https://fosdem.org/2022/archives/
 [3]: https://www.bareos.org/en/manual.html
 [4]: https://docs.bareos.org/
 [5]: https://download.bareos.org/bareos/release/
 [6]: /img/2017/03/bareos.png
 [7]: https://www.svennd.be/bareos-articles/
