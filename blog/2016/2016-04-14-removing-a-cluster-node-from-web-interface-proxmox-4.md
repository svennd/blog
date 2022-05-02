---
title: Removing a cluster node from web-interface, proxmox 4
author: svennd

date: 2016-04-14T14:54:11+00:00
url: /removing-a-cluster-node-from-web-interface-proxmox-4/
thumbnail: /img/2020/02/nightsky.jpg
categories:
  - proxmox
  - SysAdmin
tags:
  - proxmox

---
I removed a node from my cluster some time ago and for some weird reason it kept on being in my (web)interface, It showed as down all the time. Removing it from interface is surprisingly simple, but for future reference here goes :  (note this is for proxmox 3.3-5, but it seems to [work for 4.1 as well.][1])

<pre>NODENAME=node1

cp -r /etc/pve/nodes/$NODENAME /tmp
rm -r /etc/pve/nodes/$NODENAME
</pre>

happy virtualization cluster !

 [1]: https://forum.proxmox.com/threads/deleted-cluster-node-still-shows-up-in-webinterface-pve-4-1.26819/#post-134772