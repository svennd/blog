---
title: 'Bareos-sd : could not open FileStorage'
author: svennd

date: 2019-04-11T08:56:08+00:00
url: /bareos-sd-could-not-open-filestorage/
thumbnail: /img/2020/05/edward-howell-OEfm0hrY_rw-unsplash-scaled.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - bareos

---
A simple issue, but can be tricky nevertheless !

<pre>bareos-sd JobId 265: Warning: stored/mount.cc:270 Open device "FileStorage3" (/storage/block1) Volume "Full-0015" failed: ERR=stored/dev.cc:731 Could not open: /storage/block1/Full-0015, ERR=No such file or directory</pre>

While experimenting I had made the owner of /storage/block1 different from the Bareos user; This resulted in the storage deamon (bareos-sd) not able to make new files; Solving it, was chowning the directory and restarting the bareos-sd & bareos-dir :

<pre>chown bareos.bareos /storage/block1
systemctl restart bareos-sd
systemctl restart bareos-dir</pre>

&nbsp;