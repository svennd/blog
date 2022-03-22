---
title: 'Proxmox upgrading from 5.4.* -> 6.1-7'
author: svennd

date: 2020-02-12T15:29:47+00:00
url: /proxmox-upgrading-from-5-4-6-1-7/
thumbnail: /img/2020/02/nightsky.png
categories:
  - Linux
  - proxmox
  - SysAdmin
tags:
  - proxmox

---
It's the first time I'm upgrading Proxmox's; There is luckily [good documentation][1] on the upgrade path; So this is only a small summary of how I did the upgrade of a few standalone proxmox machines; (big note : this is for **non cluster proxmox**)

First I upgrade everything to the latest :

<pre>apt-get update && apt-get dist-upgrade</pre>

Should work flawless :

<pre>root@server:~# apt-get update
Ign:1 http://ftp.be.debian.org/debian stretch InRelease
Get:2 http://security.debian.org stretch/updates InRelease [94.3 kB]
Get:3 http://ftp.be.debian.org/debian stretch-updates InRelease [91.0 kB]
Hit:4 http://ftp.be.debian.org/debian stretch Release
Hit:5 http://download.proxmox.com/debian stretch InRelease
Fetched 185 kB in 0s (308 kB/s)
Reading package lists... Done
root@server:~# apt-get dist-upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
</pre>

In case you have kernel updates; its advisable to reboot the machine, so you work from the latest and greatest.

After this use the proxmox upgrade checktool :

<pre>pve5to6</pre>

This delivered :

<pre>= SUMMARY =

TOTAL:    16
PASSED:   13
SKIPPED:  2
WARNINGS: 1
FAILURES: 0</pre>

The only warning is :

<pre>WARN: 2 running guest(s) detected - consider migrating or stopping them.</pre>

On a different machine some SSH Ciphers where no longer supported; to fix this, remove them; from :

<code class="EnlighterJSRAW" data-enlighter-language="null">/root/.ssh/config</code>

<pre>Ciphers aes128-ctr,aes192-ctr,aes256-ctr,aes128-cbc</pre>

Then change the repo's being used from stretch to buster, one can use this handy one liner; unless you have a subscription then you need to also change /etc/apt/sources.list.d/pve-enterprise

<pre>sed -i 's/stretch/buster/g' /etc/apt/sources.list</pre>

after that update the list :

<pre>apt-get update</pre>

if you want minimal downtime, you could first download the updates, while the machines are still running; using :

<pre>apt dist-upgrade --download-only</pre>

make sure all machines are down now (I used the webinterface); then run the upgrade : (and pray & take a coffee)

<pre>apt dist-upgrade</pre>

Some accept's are required during the installation; (just enter to accept the defaults) these should not be essential to proxmox;

If everything is going fine; reboot and unleash the Proxmox 6.1*goodies, such as ZFS 0.8.2 🙂

 [1]: https://pve.proxmox.com/wiki/Upgrade_from_5.x_to_6.0