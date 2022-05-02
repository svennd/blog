---
title: Installing Nginx, PHP 7 on Centos 6.7
author: svennd

date: 2016-02-18T10:06:33+00:00
url: /installing-nginx-php-7-on-centos-6-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
  - webdev

---
Installing nginx and any version of PHP is not particular difficult, yet still I have to google it every time I do, so here below you find how I normally do it for virtual machines.

First I always update and install yum-cron, this will try and update nightly, great for sleep, even if I missed a [bug in a important package][1], my servers will try to fix them-self.

<!--more-->

<pre>yum update -y
yum install yum-cron
service yum-cron start
chkconfig yum-cron on</pre>

Next is some tools I install, most important :

<pre>yum install nano</pre>

Next lets get Nginx, I am now taking [mainline by default, but stable][2] is also good :

<pre>nano /etc/yum.repos.d/nginx.repo
[nginx]
name=nginx repo
# mainline : newest
baseurl=http://nginx.org/packages/mainline/centos/6/$basearch/
# stable : older, no new features
#baseurl=http://nginx.org/packages/centos/6/$basearch/
gpgcheck=0
enabled=1</pre>

Now we can go ahead and install Nginx from their repository , since Nginx is not in any "official" repository there is no problem with overlapping package names.

<pre>yum update -y
yum install nginx</pre>

Then to install PHP7 (or PHP 5.4, 5.5, 5.6), Centos has 5.3.3 as default... its kinda old. (although Centos patches bugs, so its save to use !) To get a newer version we need to install both epel repo and an external repo, I like webtatic, but there are others that will work to.

<pre>rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm</pre>

Then to install PHP and some extra's.

<pre>yum update
yum install php70w-fpm php70w-opcache php70w-gd php70w-mysql php70w-mcrypt
</pre>

note that this will install fpm to work with Nginx, for Apache, you would need _php70w._

happy coding!

 [1]: https://www.svennd.be/time-to-update-glibc-library-buffer-overfl0w/
 [2]: https://www.svennd.be/nginx-stable-is-not-so-mainline/