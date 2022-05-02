---
title: Change default storage location for Bareos
author: svennd

date: 2019-03-21T10:13:06+00:00
url: /change-default-storage-location-for-bareos/
thumbnail: /img/2020/05/hasan-almasi-8ptSlYer2uY-unsplash-1.jpg
categories:
  - SysAdmin
tags:
  - bareos

---
By default the device used by Bareos for data archival/storage is FileStorage. Meaning data stored is stored in images on spinning disks in the file structure:

<pre>/var/lib/bareos/storage</pre>

While this might be a good place for you, its tricky with this location is in root ... (full root and whatnot) You can change this in :

<pre>/etc/bareos/bareos-sd.d/device/FileStorage.conf</pre>

By changing the archive device :

<pre>Archive Device = /backup</pre>

Obviously you could make an extra device, and I'm only scratching the options you can do here, but since your root might not be terabytes in size, changing this is pretty crucial;

After changing this, you need to restart the bareos-fd :

<pre>systemctl restart bareos-fd</pre>

You can also remove the images that already are in /var/lib/bareos/storage and delete the volumes from the database :

<pre>bconsole
delete volume=Full-0001 yes</pre>

Don't forget to physically remove them (rm -f)