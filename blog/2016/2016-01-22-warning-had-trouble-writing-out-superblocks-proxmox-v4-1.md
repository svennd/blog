---
title: Warning, had trouble writing out superblocks; proxmox v4.1
author: svennd

date: 2016-01-22T12:57:16+00:00
url: /warning-had-trouble-writing-out-superblocks-proxmox-v4-1/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - proxmox
  - SysAdmin
  - ZFS

---
A small "bugfix" on proxmox 4.1 with ZFS below it, on a clean install, proxmox will fail to make a lxc (linux container). One has to create a zfs storage before containers can be made ... The error during creation:

<pre>Warning, had trouble writing out superblocks.TASK ERROR: command 'mkfs.ext4 -O mmp -E 'root_owner=0:0' /var/lib/vz/images/100-disk-1.raw' failed: exit code 144</pre>

The solution :  
Datacenter -> Add ZFS  
- id : whatever  
- zfs pool : I took rpool/ROOT but you are free in this as far as I could see  
- I did not restrict nodes, although I would not use it other then local.  
- Enable : on  
- Thin provision : no clue, I left it unmarked.

Then during the lxc creation select lxc_storage (this is the id you gave)

// update 7/04/2017

  * When importing from console : <pre>pct restore $container_id container.tar.lzo -storage lxc_storage</pre>
    
    &nbsp;</li> </ul>