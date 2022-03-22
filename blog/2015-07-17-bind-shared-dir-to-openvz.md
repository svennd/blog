---
title: 'Mount shareable dir to openvz & proxmox'
author: svennd

date: 2015-07-17T12:41:24+00:00
url: /bind-shared-dir-to-openvz/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - proxmox
  - SysAdmin
tags:
  - linux
  - proxmox

---
This one is really easy once you know it, so I like to share it with you. I wanted to move a shared directory from our storage server to a container on proxmox; However I don't want to add ~4TB on data to my regular backup, so I thought a NFS service would be needed; seems its not ! You can simply mount a directory to your -running- container!

<code class="EnlighterJSRAW" data-enlighter-language="null">mount --bind LOCAL_DIR /var/lib/vz/root/114/INSIDE_CONTAINER</code>

LOCAL_DIR : that's the directory on the "host" node, for me this was /data/groups  
INSIDE_CONTAINER : that's the directory inside the container starting from / : root so  /var/lib/vz/root/114/media/groups/ inside the container linked to /media/groups

This probably won't survive a reboot; So I added this to /etc/vz/conf/114.mount

sources :

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">#!/bin/bash
source /etc/vz/vz.conf
source ${VE_CONFFILE}
mount -n --bind /data/groups ${VE_ROOT}/media/groups
</pre>

[serverfault][1]  
[bind mounts in openvz man][2]  
[topic question @ openvz][3]

 [1]: http://serverfault.com/questions/241966/openvz-share-a-folder-between-containers
 [2]: http://wiki.openvz.org/Bind_mounts
 [3]: http://forum.openvz.org/index.php?t=msg&goto=42129&