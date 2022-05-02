---
title: Disable 2FA in Proxmox 6
author: svennd

date: 2019-11-21T10:20:49+00:00
url: /disable-2fa-in-proxmox-6/
thumbnail: /img/2020/02/nightsky.jpg
categories:
  - Linux
  - proxmox
  - SysAdmin
tags:
  - proxmox

---
I was checking the new version of Proxmox, when I noticed there is an option to enable 2FA; (see the [docs][1]) It works really fine, but then I realized my colleague's probably don't have the key. To disable however, I couldn't find it online; So I digged in the guts of Proxmox <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/pve/user.cfg</code> there is a line similar to :

<pre>user:root@pam:1:0:::user.name@mail.ext::x!oath:</pre>

I changed this to :

<pre>user:root@pam:1:0:::user.name@mail.ext:::</pre>

&nbsp;

That change let me log in without the 2FA.

&nbsp;

 [1]: https://pve.proxmox.com/pve-docs/pve-admin-guide.html#two_factor_authentication