---
title: Auto upgrade on Centos 6/7 using yum-cron
author: svennd

date: 2016-06-23T12:45:16+00:00
url: /setup-auto-upgrade-on-centos-67/
thumbnail: /img/2020/04/centos.jpg
categories:
  - Linux
  - SysAdmin

---
&nbsp;

<pre>Using Centos Stream 8 ? This won't work anymore, it changed to [dnf-automatic](https://www.svennd.be/auto-upgrade-on-centos-stream-8-using-dnf-automatic/) !</pre>

This is one of those things I do when I enter a new server/virtual machine. While this could cause issue's down the road, (updates are always a bit of a guess) Most simple advice to give to problems in Linux is, "did you update ?". In Centos its pretty easy to set this up automatic updates, so no server should be without this essential part of configuration. Another big point is security, while this isn't per definition more secure, its better to have a bad update then to have a few 100 MB of not done updates cause they "might break something".

1) "update"  
Seems so logically, but still update everything before you let yum-cron loose.

<pre>yum update</pre>

2) Install the magic  
Its as easy as pie.

<pre>yum install yum-cron</pre>

3) configure  
Configuration file for Centos 6 is located <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/sysconfig/yum-cron</code> for Centos 7 its located at <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/yum/yum-cron.conf</code> (a more logically location) Go through the settings, and decide whats best for you, personally I leave most as-if, only changing (Centos7) :

<pre>apply_updates = yes</pre>

Since I want them installed nightly if available.

4) start the service & enable at reboot

<pre># service yum-cron start
Redirecting to /bin/systemctl start  yum-cron.service
# chkconfig yum-cron on
Note: Forwarding request to 'systemctl enable yum-cron.service'.
</pre>

Enjoy ! From now on, your machine will be up-to-date... no more lagging behind! If an error should happen cause of updates, you only have to look back a few days in :

<pre>/var/log/yum.log</pre>

Happy updating!