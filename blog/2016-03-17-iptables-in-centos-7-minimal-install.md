---
title: Disable firewalld, use Iptables in Centos 7 minimal install
author: svennd

date: 2016-03-17T10:49:14+00:00
url: /iptables-in-centos-7-minimal-install/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
By default Centos 7 comes with _firewallD_ installed, which is a wrapper around _iptables_. That's great for people who don't understand iptables, but its as cryptic or even worse as _iptables_ so why bother ? On a minimal install, there is no _firewalld_, _iptables_ is there but is missing iptables-service package. This is a short howto to get _iptables_ up & running, in either minimal or full install.

#### disable firewalld

on a minimal its not there (hence it will fail) but be sure to check it :

<pre># systemctl disable firewalld
Failed to execute operation: Access denied</pre>

if it is :

<pre>systemctl disable firewalld
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
Removed symlink /etc/systemd/system/basic.target.wants/firewalld.service.
</pre>

#### Install services

This installs iptables-services and will make it possible to "save" it again. (I also set it to start after reboot)

<pre>yum install iptables-services
systemctl enable iptables</pre>

and voila :

<pre>service iptables save
service ip6tables save</pre>

ready to firewall up!