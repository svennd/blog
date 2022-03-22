---
title: 'Proxmox VE 6.1 fix update & upgrades'
author: svennd

date: 2020-02-11T12:17:04+00:00
url: /proxmox-ve-6-1-fix-update-upgrades/
thumbnail: /img/2020/02/nightsky.png
categories:
  - Linux
  - proxmox
  - SysAdmin
tags:
  - proxmox

---
Here we are again, 'a brand new' [Proxmox : 6.1][1] based on debian Buster (10.2) including ZFS 0.8.2 (zfs on Linux). However updating by default will fail due to the inclusion of only the enterprise (paying) repo's; They do however offer free repo's that are marked as testing; To activate those and get rid of this ugly error, follow along;

remove or disable pve-enterprise repo :

<pre>rm -f /etc/apt/sources.list.d/pve-enterprise.list</pre>

add to <code class="EnlighterJSRAW" data-enlighter-language="null">nano /etc/apt/sources.list</code>

<pre>deb http://download.proxmox.com/debian buster pve-no-subscription</pre>

Now you can run those updates 🙂  take note only _<span class="enlighterEnlighterJS EnlighterJS"><span class=""> apt-get update</span></span>_ and _<span class="enlighterEnlighterJS EnlighterJS"><span class="">apt-get dist-upgrade</span></span>_ are supported by Proxmox !

Update & upgrade

<pre>apt-get update
apt-get dist-upgrade</pre>

happy virtualizing !

 [1]: https://www.proxmox.com/en/news/press-releases/proxmox-ve-6-1