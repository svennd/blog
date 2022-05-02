---
title: 'proxmox : fix updates'
author: svennd

date: 2015-07-16T10:44:15+00:00
url: /proxmox-fix-updates/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - proxmox
  - SysAdmin
tags:
  - proxmox
  - updates

---
I love [proxmox](https://www.proxmox.com/en/), its cool it works and its very user friendly. The only pain point is, they try to sell their  subscription really hard. In such an attempt they even change the default update location to something that will throw an error when not having a subscription!

<pre>Err https://enterprise.proxmox.com jessie/pve-enterprise amd64 Packages
  HttpError401
Get:22 https://enterprise.proxmox.com jessie/pve-enterprise Translation-en_US [401 B]
Ign https://enterprise.proxmox.com jessie/pve-enterprise Translation-en_US
Get:23 https://enterprise.proxmox.com jessie/pve-enterprise Translation-en [401 B]
Ign https://enterprise.proxmox.com jessie/pve-enterprise Translation-en
Fetched 572 kB in 2s (231 kB/s)
W: Failed to fetch https://enterprise.proxmox.com/debian/dists/jessie/pve-enterprise/binary-amd64/Packages  HttpError401

E: Some index files failed to download. They have been ignored, or old ones used instead.
</pre>

Changing them is easy enough :

<code class="EnlighterJSRAW" data-enlighter-language="null">nano /etc/apt/sources.list</code>

add

**note : this is for wheezy**

<pre class="EnlighterJSRAW" data-enlighter-language="null" data-enlighter-linenumbers="false"># pve updates
deb http://download.proxmox.com/debian wheezy pve-no-subscription</pre>

**For jessie :**

<pre>deb http://download.proxmox.com/debian jessie pve-no-subscription</pre>

Also remove this one :

<code class="EnlighterJSRAW" data-enlighter-language="null">rm /etc/apt/sources.list.d/pve-enterprise.list</code>

If you now run a  <code class="EnlighterJSRAW" data-enlighter-language="shell">apt-get update && apt-get dist-upgrade</code> no errors should be there ! ([dist-upgrade][1] !)

&nbsp;

_Note_ _: There is also a official wiki, where one can see the same changes : [proxmox wiki][2]_

 [1]: https://www.svennd.be/proxmox-upgrade-issues/
 [2]: https://pve.proxmox.com/wiki/Package_repositories
