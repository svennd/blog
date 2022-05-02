---
title: 'mount: unknown filesystem type ‘nfs’ (proxmox)'
author: svennd

date: 2015-08-13T07:41:46+00:00
url: /mount-unknown-filesystem-nfs-proxmox/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - proxmox
  - SysAdmin
tags:
  - nfs

---
Awww cute little proxmox container doesn't know nfs. (CentOS release 6.7 (Final))

<pre>[root@server /]# mount -o ro -t nfs server:/data /media/
mount: unknown filesystem type 'nfs'
</pre>

Well its new its young, lets give it some experience with nfs :

<pre>yum install nfs-utils</pre>

So are we ready yet ?

<pre>[root@server/]# mount -o ro -t nfs  server:/data /media/
mount.nfs: No such device
</pre>

Wut ? Ah, since this is not "our" kernel, we can't access the kernel modules and load the nfs module : -[no reason to panic](https://unix.stackexchange.com/questions/119725/fatal-module-nfs-not-found)-

<pre>[root@server/]# modprobe nfs
Fatal: module nfs not found</pre>

This is done using : _ _

<pre>vzctl set OPEN_VZ_ID --feature nfs:on --save</pre>

_note : The [openVZ](https://wiki.openvz.org/NFS_server_inside_container) manual suggests to add nfsd as a feature, I'm not sure why, since "d" mostly is used to indicate its a deamon; perhaps its a typo, or on other systems its like that, but I did not found that to be working; _

_note 2 : if you don't like the command, just add it directly to the config file, for my proxmox installation that is in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/pve/openvz/xxx.conf</code> add : <code class="EnlighterJSRAW" data-enlighter-language="null">FEATURES="nfs:on"</code>  
_ 

after this, you need to restart the container <code>vzctl restart OPEN_VZ_ID</code> after that you can enter again and [mounting the nfs share](https://svennd.be/mount-read-only-nfs/) should work.

<pre>mount -o ro -t nfs server:/data media/</pre>
