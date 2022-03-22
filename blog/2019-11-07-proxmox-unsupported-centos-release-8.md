---
title: proxmox unsupported centos release 8
author: svennd

date: 2019-11-07T12:28:38+00:00
url: /proxmox-unsupported-centos-release-8/
thumbnail: /img/2020/02/nightsky.png
categories:
  - proxmox
  - SysAdmin
tags:
  - centos
  - proxmox

---
Finally got time to give Centos 8 a spin in a Proxmox Linux container (lxc); I downloaded the template, and started a container only to find its not possible :

<pre>extracting archive '/var/lib/vz/template/cache/centos-8-default_20191016_amd64.tar.xz'
Total bytes read: 673566720 (643MiB, 37MiB/s)
Detected container architecture: amd64
TASK ERROR: unable to create CT 100 - unsupported centos release 'CentOS Linux release 8.0.1905 (Core) '</pre>

I tried update & upgrade :

<pre>apt-get update && apt-get dist-upgrade</pre>

To no avail; Luckely Geekshack SecLabs (dead link) and his sed wizardry provided a workaround :

<pre>sed -i 's/ &lt; 8) {/ &lt; 9) {/' /usr/share/perl5/PVE/LXC/Setup/CentOS.pm</pre>

Then restart the pve daemon :

<pre>systemctl restart pveproxy pvedaemon</pre>

and bam, container is installed  :

<pre>extracting archive '/var/lib/vz/template/cache/centos-8-default_20191016_amd64.tar.xz'
Total bytes read: 673566720 (643MiB, 38MiB/s)
Detected container architecture: amd64
[-cut-]
TASK OK</pre>
