---
title: Proxmox upgrade issue’s
author: svennd

date: 2017-02-08T12:48:09+00:00
url: /proxmox-upgrade-issues/
thumbnail: /img/2020/02/nightsky.png
categories:
  - proxmox
  - SysAdmin
tags:
  - proxmox

---
After upgrading the latest proxmox; (apt-get update && apt-get upgrade) I received this error :

<pre>Errors were encountered while processing:
pve-manager
E: Sub-process /usr/bin/dpkg returned an error code (1)</pre>

<!--more-->

It even got worse trying to find out what was happening :

<pre>systemctl status pvedaemon.service
● pvedaemon.service - PVE API Daemon
   Loaded: loaded (/lib/systemd/system/pvedaemon.service; enabled)
   Active: active (running) (Result: exit-code) since Thu 2017-02-02 10:02:50 CET; 5 days ago
  Process: 25724 ExecReload=/usr/bin/pvedaemon restart (code=exited, status=2)
Main PID: 3124 (pvedaemon)
   CGroup: /system.slice/pvedaemon.service
           ├─ 3124 pvedaemon
           ├─11299 pvedaemon worker
           ├─11689 pvedaemon worker
           └─26073 pvedaemon worker

Feb 07 10:19:49 rocky pvedaemon[25724]: Compilation failed in require at /usr/share/perl5/PVE/API2/Cluster.pm line 13.
Feb 07 10:19:49 rocky pvedaemon[25724]: BEGIN failed--compilation aborted at /usr/share/perl5/PVE/API2/Cluster.pm line 13.
Feb 07 10:19:49 rocky pvedaemon[25724]: Compilation failed in require at /usr/share/perl5/PVE/API2.pm line 13.
Feb 07 10:19:49 rocky pvedaemon[25724]: BEGIN failed--compilation aborted at /usr/share/perl5/PVE/API2.pm line 13.
Feb 07 10:19:49 rocky pvedaemon[25724]: Compilation failed in require at /usr/share/perl5/PVE/Service/pvedaemon.pm line 8.
Feb 07 10:19:49 rocky pvedaemon[25724]: BEGIN failed--compilation aborted at /usr/share/perl5/PVE/Service/pvedaemon.pm line 8.
Feb 07 10:19:49 rocky pvedaemon[25724]: Compilation failed in require at /usr/bin/pvedaemon line 11.
Feb 07 10:19:49 rocky pvedaemon[25724]: BEGIN failed--compilation aborted at /usr/bin/pvedaemon line 11.
Feb 07 10:19:49 rocky systemd[1]: pvedaemon.service: control process exited, code=exited status=2
Feb 07 10:19:49 rocky systemd[1]: Reload failed for PVE API Daemon.</pre>

Another apt-get upgrade made things worse :

<pre>Job for pvedaemon.service failed. See 'systemctl status pvedaemon.service' and 'journalctl -xn' for details.
dpkg: error processing package pve-manager (--configure):
subprocess installed post-installation script returned error exit status 1
dpkg: dependency problems prevent configuration of proxmox-ve:
proxmox-ve depends on pve-manager; however:
  Package pve-manager is not configured yet.
dpkg: error processing package proxmox-ve (--configure):
dependency problems - leaving unconfigured
Setting up tcpdump (4.9.0-1~deb8u1) ...
Setting up zfsutils-linux (0.6.5.8-pve14~bpo80) ...
Installing new version of config file /etc/cron.d/zfsutils-linux ...
Installing new version of config file /etc/sudoers.d/zfs ...
Setting up zfs-initramfs (0.6.5.8-pve14~bpo80) ...
Setting up zfs-zed (0.6.5.8-pve14~bpo80) ...
Setting up libnvpair1 (0.6.5.8-pve14~bpo80) ...
Setting up libuutil1 (0.6.5.8-pve14~bpo80) ...
Setting up libzfs2 (0.6.5.8-pve14~bpo80) ...
Setting up libzpool2 (0.6.5.8-pve14~bpo80) ...
Setting up zfsutils (0.6.5.8-pve14~bpo80) ...
Processing triggers for libc-bin (2.19-18+deb8u7) ...
Processing triggers for initramfs-tools (0.120+deb8u2) ...
update-initramfs: Generating /boot/initrd.img-4.4.35-2-pve
Errors were encountered while processing:
pve-manager
proxmox-ve
E: Sub-process /usr/bin/dpkg returned an error code (1)</pre>

and what version you ask :

<pre>pveversion -v
Can't locate PVE/RESTEnvironment.pm in @INC (you may need to install the PVE::RESTEnvironment module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.20.2 /usr/local/share/perl/5.20.2 /usr/lib/x86_64-linux-gnu/perl5/5.20 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.20 /usr/share/perl/5.20 /usr/local/lib/site_perl .) at /usr/share/perl5/PVE/RPCEnvironment.pm line 6.
BEGIN failed--compilation aborted at /usr/share/perl5/PVE/RPCEnvironment.pm line 6.
Compilation failed in require at /usr/share/perl5/PVE/API2/APT.pm line 20.
BEGIN failed--compilation aborted at /usr/share/perl5/PVE/API2/APT.pm line 20.
Compilation failed in require at /usr/bin/pveversion line 7.
BEGIN failed--compilation aborted at /usr/bin/pveversion line 7.</pre>

For some unknown (to me) reason the updated packages required -not defined- a package only being updated/installed on a dist-upgrade. So for some reason Proxmox dev's broke a dependency ... sadly its not the first time this has happened to me. I'm wondering if its a method to push you to a paying license ? Anyway test before you hit your production servers !

To solve these issue's run :

<pre>apt-get update
apt-get dist-upgrade</pre>

&nbsp;

_// update _

As discussed on the [Proxmox forum][1] the last two commands are the only way supported way to [update proxmox][2], just like Martin pointed out in a comment below, they don't support _apt-get upgrade._

 [1]: https://forum.proxmox.com/threads/upgrade-issues.32727/#post-162695
 [2]: https://pve.proxmox.com/wiki/Downloads#Update_a_running_Proxmox_Virtual_Environment_4.x_to_latest_4.4