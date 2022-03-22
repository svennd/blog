---
title: install supermicro’s IPMI
author: svennd

date: 2017-01-05T09:23:51+00:00
url: /install-supermicros-ipmi/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
IPMI is cool, this is my manual to "set it up" on Centos (RHEL) and Debian (Ubuntu, Proxmox, ...)

**Centos**

<pre>yum install ipmitool</pre>

or on older machines (Centos 5)

<pre>yum install OpenIPMI OpenIPMI-toolschkconfig ipmi</pre>

On Centos 7.3 The modules required for the ipmi device, where no longer loaded automatically. This can be fixed manually by executing :

<pre>modprobe ipmi_msghandler
modprobe ipmi_devintf
modprobe ipmi_si</pre>

You can auto-load these modules at boot by creating a file : <code class="EnlighterJSRAW" data-enlighter-language="null">/usr/lib/modules-load.d/ipmi.conf</code> with the content :

<pre># auto load ipmi modules during boot
ipmi_msghandler
ipmi_devintf
ipmi_si</pre>

**Debian**

<pre>apt-get install openipmi ipmitool</pre>

also need to load these modules for some reason :

<pre>modprobe ipmi_msghandler
modprobe ipmi_devintf
modprobe ipmi_si</pre>

Next I setup LAN setup and change the password, I mostly do that using the web-interface, but its possible to do from the console.

<pre># give information on ipmi (ip, etc.)
ipmitool lan print 1

# create user and set password
ipmitool user set name 2 admin
ipmitool user set password 2</pre>

&nbsp;

changes :

  * on Centos 7.3 I needed to add the modules to auto-load (08/03/2017)