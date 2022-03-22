---
title: 'Centos 8 : setup auto updates'
author: svennd

date: 2021-02-02T10:30:13+00:00
url: /centos-8-setup-auto-updates/
thumbnail: /img/2020/04/centos.jpg
categories:
  - Linux
  - SysAdmin

---
A quick howto install automatic updates on Centos 8 using the new dnf command;

Install :

<pre>dnf install dnf-automatic -y</pre>

Config : 

<pre>nano /etc/dnf/automatic.conf</pre>

from

<pre>apply_updates = no</pre>

to 

<pre>apply_updates = yes</pre>

Add to systemd : 

<pre>systemctl enable --now dnf-automatic.timer
</pre>

Check / verify : 

<pre>$ cat /etc/systemd/system/timers.target.wants/dnf-automatic.timer
$ systemctl list-timers dnf-* | grep automatic
</pre>