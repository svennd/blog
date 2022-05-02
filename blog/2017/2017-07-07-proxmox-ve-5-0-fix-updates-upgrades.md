---
title: Proxmox VE 5.0 fix updates / upgrades
author: svennd

date: 2017-07-07T13:01:07+00:00
url: /proxmox-ve-5-0-fix-updates-upgrades/
thumbnail: /img/2020/02/nightsky.jpg
categories:
  - Linux
  - proxmox
  - SysAdmin
tags:
  - proxmox

---
Woohoo, Proxmox VE 5.0 has been released, this version is based on Debian 9. (Linux Kernel 4.10) It includes allot of new features, but sadly updates are still pointing to the enterprise repository for updates. This results in ugly error message when trying _apt-get update_ such as :

<pre>W: The repository 'https://enterprise.proxmox.com/debian/pve stretch Release' does not have a Release file.
N: Data from such a repository can't be authenticated and is therefore potentially dangerous to use.
N: See apt-secure(8) manpage for repository creation and user configuration details.
E: Failed to fetch https://enterprise.proxmox.com/debian/pve/dists/stretch/pve-enterprise/binary-amd64/Packages  401  Unauthorized
E: Some index files failed to download. They have been ignored, or old ones used instead.</pre>

To fix this, its similar to [V3.X and V4.X.][1] We need to add one repository : <code class="EnlighterJSRAW" data-enlighter-language="null">nano /etc/apt/sources.list</code>

add :

<pre>deb http://download.proxmox.com/debian stretch pve-no-subscription</pre>

Then disable or remove

<pre>rm -f /etc/apt/sources.list.d/pve-enterprise.list</pre>

(for disable add a # in front of the first line starting with deb)

Now you can run those updates 🙂  take note only  <code class="EnlighterJSRAW" data-enlighter-language="null">apt-get update</code> and <code class="EnlighterJSRAW" data-enlighter-language="null">apt-get dist-upgrade</code> are supported by Proxmox !

Now I dislike the way Proxmox pushes people to take a subscription and there pricing method, but it is an amazing piece of free software! Well done Proxmox devs !

 [1]: https://www.svennd.be/proxmox-fix-updates/