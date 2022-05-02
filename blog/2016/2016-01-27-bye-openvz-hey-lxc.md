---
title: Bye OpenVZ, Hey LXC
author: svennd

date: 2016-01-27T13:53:35+00:00
url: /bye-openvz-hey-lxc/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - proxmox
  - SysAdmin

---
  [![linux-containers-logo](/img//2015/07/25449712-1.png)](https://linuxcontainers.org)


I recently switched 2/4 Proxmox nodes to the latest build (V4). On those [OpenVZ][1] has made place for [LXC][2]. The reason why, I am not sure 'bout. I believe it was something with kernel modules and licensing, anyway it changed. Also all the commands have changed, not really good for a efficiency, now we have both systems in place, it will take some time to swap. Anyways this definite helps :

<!--more-->

&nbsp;

add in ~/.bashrc

<pre>alias vzctl='echo "use lxc-ls, lxc-info -n ID"'
alias vzlist='lxc-ls;echo "use lxc-ls; lxc-info -n ID"'</pre>

try it out :

<pre>source ~/.bashrc
vzlist
vzctl start 123</pre>

Jeeej bash hacks ftw!

 [1]: https://openvz.org
 [2]: https://linuxcontainers.org/
