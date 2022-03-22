---
title: 'full ZFS filesystem : No space left on device'
author: svennd

date: 2017-01-03T07:28:19+00:00
url: /full-zfs-filesystem-no-space-left-on-device/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - zfs

---
Starting the new year with :

<pre>No space left on device</pre>

I looked to the usual suspects (/tmp, /data, ...)  but nothing excessive ... It was my [sanoid][1] setup, woops! A gazillion snapshots ! For some reason one of my containers changes around ~300 Gb a day, and guess who keeps 60 days of snapshots, on top of 6 months snapshots ? -> you guessed right.

Its annoying ZFS does not warn you, or that sanoid doesn't take care, not to fill it up 100% . Not that's its there task, but still, it was holidays for everyone guys ! So how to fix this ? Simply remove excessive snapshots (those can be deleted)

<pre>zfs destroy jbod1/users@autosnap_2016-12-09_14:00:01_hourly</pre>

Note that during a 100% full you can't remove data due to Copy On Write (COW) so rm\`ing a big file would result in :

<pre># rm /that/big/file
Can't remove /that/big/file. No space left on the device.</pre>

What you can do (if you can't remove snapshots, or you just filled it up the old fashion way)

<pre>echo &gt; /that/big/file</pre>

Credits for this great solution go to [this guy][2].

Best wishes for 2017 🙂

 [1]: https://www.svennd.be/zfs-snapshots-of-proxmox-using-sanoid/
 [2]: https://forums.freenas.org/index.php?threads/disk-full-cant-delete-any-files-please-help.12252/