---
title: Setting up zpool scrub every 2 months
author: svennd

date: 2021-02-09T15:00:55+00:00
url: /setting-up-zpool-scrub-every-2-months/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - ZFS

---
So scrubbing ZFS: Giving [ZFS a moment to sit down and think about its behavior][1]. It should happen now and again, but if everything is going smooth sailing we tend to forget to do this; With the ever increasing amount of ZFS servers around, it tends to be a hassle, so this is a perfect candidate for automation ! [Details on the scrubbing][2] 🙂 

I used : `/opt/scrub.sh`

<pre>#!/bin/sh -eu

# you can stop/pause a scrub
# using : zpool -p poolname

# report
logger "zfs" "cron scrub initiated"

# Scrub all healthy pools.
zpool list -H -o health,name 2&gt;&1 | \
        awk 'BEGIN {FS="\t"} {if ($1 ~ /^ONLINE/) print $2}' | \
while read pool
do
        zpool scrub "$pool"
done
</pre>

The script is part of the [Ubuntu package discussed here;][3]

Don't forget permissions : `chmod 755 /opt/scrub.sh`

in `nano /etc/crontab` :

<pre># * * * * * user-name command to be executed

0 0 1 */2 * root /opt/scrub.sh
</pre>

This should scrub all the ZFS pools once every 2 months; Happy scrubbing 😉

 [1]: https://blogs.oracle.com/wonders-of-zfs-storage/disk-scrub-why-and-when-v2
 [2]: https://pthree.org/2012/12/11/zfs-administration-part-vi-scrub-and-resilver/
 [3]: https://bugs.launchpad.net/ubuntu/+source/zfs-linux/+bug/1860228