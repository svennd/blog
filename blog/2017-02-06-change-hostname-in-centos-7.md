---
title: change hostname in Centos 7
author: svennd

date: 2017-02-06T14:00:31+00:00
url: /change-hostname-in-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin

---
It's nice to give servers a name, while web-01, db-01, lims-01,... are all valid names. It's a lot more fun to name them coffee names (breva, macchiato, ristretto,...) or our new generation are named after 'mythical beast/monsters' (wendigo, elf,...) although its not to strict, and we allow for TV characters (like Lagertha from vikings) also. Anyway I strongly believe in creating a bond between human (sysadmin, devops, ...) and machine. It helps for a good TCP communication :). This is how you can do it on Centos 7. (perhaps there are more option)

Set the name using : _hostnamectl set-hostname lagertha_

<pre>hostnamectl set-hostname $name</pre>

Validate using : _hostnamectl status_

<pre>[root@prothserver ~]# hostnamectl status
   Static hostname: lagertha
         Icon name: computer-server
           Chassis: server
        Machine ID: 4a3901cperhaps_this_is_privateadb
           Boot ID: bthis_for_sure_is_not_here7613537
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-514.el7.x86_64
      Architecture: x86-64
</pre>

What kind of funky names do you name you're server army ?