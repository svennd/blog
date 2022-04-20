---
title: 'vzquota : (error) quota file is corrupted, Proxmox 3.4'
author: svennd

date: 2018-08-10T07:50:30+00:00
url: /vzquota-error-quota-file-is-corrupted-proxmox-3-4/
thumbnail: /img/2020/02/nightsky.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - proxmox

---
Oh noes, while starting a container, it failed cause of a corrupt quota file...

<pre>Starting container ...
vzquota : (error) quota file is corrupted
vzquota on failed [4]
TASK ERROR: command 'vzctl start 114' failed: exit code 60</pre>

The fix is to remove it and rebuild it (this takes a long time, depending on the size of the container)

<pre># this failed
vzquota off 114

# delete the quota
quota drop 114</pre>

We can rebuild the quota using <code class="EnlighterJSRAW" data-enlighter-language="null">vzquota</code> but that is done automatically if you restart the container :

<pre># vzctl start 114
Starting container ...
Initializing quota ...</pre>

The vzquota currently running is : (for reference)

<pre>/usr/sbin/vzquota init 114 -b 8388608100 -B 9227468900 -i 1600000100 -I 1760000100 -p /data/private/114 -e 0 -n 0 -s 0</pre>

Sit back and relax, cause this will take time.