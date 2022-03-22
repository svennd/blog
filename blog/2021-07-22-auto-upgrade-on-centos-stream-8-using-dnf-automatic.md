---
title: Auto upgrade on CentOS Stream 8 using dnf-automatic
author: svennd

date: 2021-07-22T10:28:47+00:00
url: /auto-upgrade-on-centos-stream-8-using-dnf-automatic/
thumbnail: /img/2020/04/centos.jpg
categories:
  - Linux
  - SysAdmin

---
I wrote a [short guide][1] on how to set up automatic updates/upgrades on CentOS 6 and 7, sadly in CentOS Stream 8, this no longer works. Even though yum is still working, it won't find anything providing yum-cron.

<pre>[root@centos8 ~]# yum search yum-cron
Last metadata expiration check: 0:12:24 ago on Thu 22 Jul 2021 10:36:32 AM CEST.
No matches found.
[root@centos8 ~]# yum whatprovides yum-cron
Last metadata expiration check: 0:12:29 ago on Thu 22 Jul 2021 10:36:32 AM CEST.
Error: No Matches found
</pre>

So let's install the alternative : dnf-automatic

<pre>yum install dnf-automatic</pre>

Then check & change the config :

<pre>nano /etc/dnf/automatic.conf</pre>

I, for one want to apply all updates, this is a server and I definitely don't log in daily to check for updates. (I even think its a weird default)

<pre># change to yes
apply_updates = yes
</pre>

Then activate the timer to run it :

<pre>systemctl enable --now dnf-automatic-install.timer
</pre>

Check if the timer is installed fine :

<pre>systemctl list-timers</pre>

more info : https://dnf.readthedocs.io/en/latest/automatic.html

Hastle free updates 🙂

 [1]: https://www.svennd.be/setup-auto-upgrade-on-centos-67/
