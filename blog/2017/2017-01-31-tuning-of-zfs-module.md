---
title: Tuning of ZFS module
author: svennd

date: 2017-01-31T12:15:17+00:00
url: /tuning-of-zfs-module/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - zfs

---
#### **Tuning of ZFS module**

The more difficult part of ZOL is the fact that there are plenty of tune able kernel module parameters, and hence ZFS can be used in many kinds of systems for many different reasons. (laptops, file-servers, database-servers, file-clusters) However some of the parameters come bad out of the box for file serving systems. Here are some of the options that might help you tune ZFS on Linux.

All these parameters are set in :

<pre>/etc/modprobe.d/zfs.conf</pre>

##### zfs\_arc\_min/zfs\_arc\_max

The ARC (Adaptive Replacement Cache) is a cache that's used to speed up read actions, (also async writes can be grouped here before flushed to disk (?)) by default _zfs\_arc\_max_ is going to take (all physical memory - 1 GB) if the total physical memory is larger then 4GB. At least if I can believe [this document][1] however I think ZOL uses 75% by default. On a storage-only device, this can be increased, however on anything else, while the memory will be used for ARC, the ARC size will lower when applications require it. The only disadvantage is; some applications will require a certain amount of memory to start, or request memory on a rate that is quicker then ARC size can lower, resulting in a crash/swapping.  The _zfs\_arc\_min _is the smallest value the ARC will become when there is memory pressure. You could lower it on for example laptops/desktops that only use ZFS for a part of the system (backup storage).

The options are expressed in bytes...

<pre># i did not adapt zfs_arc_min
# options zfs_arc_min=

# i increased zfs_arc_max as this is a 128GB memory machine only serving files
options zfs_arc_max=100000000000</pre>

Possible options to check are arcstat :

<pre># /opt/zfs/arcstat.py -f "time,read,hit%,hits,miss%,miss,arcsz,c" 1
    time  read  hit%  hits  miss%  miss  arcsz     c
16:21:42   52K    98   52K      1   716    48G   48G
16:21:43   69K    98   68K      1   937    48G   48G
16:21:44   38K    98   38K      1   459    48G   48G
16:21:45   41K    98   40K      1   623    48G   48G
16:21:46   47K    98   46K      1   483    48G   48G</pre>

As you see, ZFS dynamically adapts ARC, seems my server is not working very hard at this moment, and hence the arcsize is only ~48GB large. Useful stats are the hit%. If you get a low value, this would mean ARC is not catching much requests, so increasing might help when you expect read requests should be able to be cached.

It is also possible to disable ARC per dataset, or reduce what it is used for, of course this will slow any file action hugely and you should only do it when you really don't care about speed in that particular data-set.  There is a _primarycache_ (RAM), and a _secondarycache_ (cache devices, such as SSD's), by default they are both used for both metadata and data. The options are :

  * all : both metadata and data are cached
  * metadata : only metadata is cached (file names, sizes, attributes, ...)
  * none : no cache is used

<pre># zfs get all jbod1 | grep cache
jbod1  primarycache          all                    default
jbod1  secondarycache        all                    default
</pre>

I left as is (default is all), and would advice you (based on [this mailinglist)][2] to also leave it default. A much better option is to lower the _zfs\_arc\_max_ since ZFS will still be able to cache some of the data/metadata.

##### **zfs\_vdev\_[async|sync]\_[read|write]\_[min|max]_active**

ZFS has five I/O queues for each of the different I/O types :

  * sync reads : normal read operation by applications
  * async reads : reads by ZFS prefetcher, this will try to "guess" what writes an application might need and request them and cache them
  * sync writes : a write that requires fsync(); to be written to "long term storage", basically disk or when a separate ZIL is available written to ZIL (intent log).
  * async writes :  a 'normal' write. (called bulk writes of dirty data)
  * scrub :  those are scrub and resilver operation, scrub checks for wrong checksums, resilver tries to repair. (resilver : I would say, raid rebuild)

This is straight from the [ZFS on Linux sourcecode][3] :

> The ratio of the queues' max\_actives determines the balance of performance between reads, writes, and scrubs. E.g., increasing zfs\_vdev\_scrub\_max_active will cause the scrub or resilver to complete more quickly, but reads and writes to have higher latency and lower throughput

These value's however are low for modern hardware and can be increased. Now I don't completely understand what is at play here, so feel free to research on this, but these value's seem to increase throughput at cost of latency. For example; Scrubbing is now a few factors quicker ... from a few days to 18h / ~100TB. (that makes it I can cron it, more on [scrub frequently][4])

Each of the _zfs\_vdev\_\*\_\*\_max_active_ limits the number of IO's issues to a single vdev.  Finding the right value's can be done by monitoring IO throughput and latency under load, and increase the value until you find the point where throughput no longer increased and latency is still acceptable. These are the value's I use :

<pre># increase them so scrub/resilver is more quickly at the cost of other work
options zfs zfs_vdev_scrub_min_active=24
options zfs zfs_vdev_scrub_max_active=64

# sync write
options zfs zfs_vdev_sync_write_min_active=8
options zfs zfs_vdev_sync_write_max_active=32

# sync reads (normal)
options zfs zfs_vdev_sync_read_min_active=8
options zfs zfs_vdev_sync_read_max_active=32

# async reads : prefetcher
options zfs zfs_vdev_async_read_min_active=8
options zfs zfs_vdev_async_read_max_active=32

# async write : bulk writes
options zfs zfs_vdev_async_write_min_active=8
options zfs zfs_vdev_async_write_max_active=32</pre>

At current writing these are the default value's in the ZoL repo:

<pre>zfs_vdev_sync_read_min_active = 10;
zfs_vdev_sync_read_max_active = 10;
zfs_vdev_sync_write_min_active = 10;
zfs_vdev_sync_write_max_active = 10;
zfs_vdev_async_read_min_active = 1;
zfs_vdev_async_read_max_active = 3;
zfs_vdev_async_write_min_active = 1;
zfs_vdev_async_write_max_active = 10;
zfs_vdev_scrub_min_active = 1;
zfs_vdev_scrub_max_active = 2;</pre>

##### **zfs\_dirty\_data\_max\_percent**

As far as I understand, dirty data are non-sync writes, so basically the applications says to the file system, write this down, but doesn't wait or expects a reply. Since I have a L2ARC and ARC, this should cache it up first and only then we should start writing it to "slow" disks. This value limits the amount of data that can be at any given time in the pool. (the cap is at 25% of physmem)

Default out of the box is 10%;

<pre>options zfs zfs_dirty_data_max_percent=40</pre>

##### **zfs\_top\_maxinflight**

Maximum number of scrub I/O per top-level vdev, by default 32. Increases zfs scrub speed. (at what cost, no idea)

<pre>options zfs zfs_top_maxinflight=320
</pre>

##### **zfs\_txg\_timeout**

There is a time before async writes are written to disk, this makes it possible for ZFS to write a larger piece. The old default was 30 seconds, but due to fluctuating write performance on some machines was lowered to 5 seconds. Decent servers should be able to hold a bit of data, so I increased it to 15 seconds, more might be fine, depending on the workload.

<pre>options zfs zfs_txg_timeout=15</pre>

##### **zfs\_vdev\_scheduler**

I see this online frequently where this setting is set; I contacted an expert and  **Noop** and **Deadline** are both fine.  You can read more about [noop scheduler][5] and the [deadline scheduler][6], but I don't understand enough to give a fair value. I did get the feeling deadline is more focused on databases pattern but I could be wrong. I did not change it.

<pre># default : noop
options zfs zfs_vdev_scheduler=deadline</pre>

##### **zfs\_prefetch\_disable**

ZFS can predict what data will be requested next, and can cache this until the request comes, this is easier and faster for spinning disks, by default its disabled (it was a surprise to me) setting this value to zero will activate the setting.

<pre># use the prefetch method
options zfs zfs_prefetch_disable=0</pre>

##### l2arc\_write\_max

Only applies if you have cache device such as a ssd, when ZFS was created, ssd's where new and could only be written to a few times, so zfs has some prehistoric limits to save the SSD of the hard labor. l2arc\_write\_max is such a value, by default only 8mb/s can be written to the ssd. Clearly you can increase this. (at the cost of more SSD use)

<pre># max write speed to l2arc
# tradeoff between write/read and durability of ssd (?)
# default : 8 * 1024 * 1024
# setting here : 500 * 1024 * 1024
options zfs l2arc_write_max=524288000</pre>

##### l2arc_headroom

Number of max devices writes to precache, can be increased.

<pre># number of max device writes to precache
# default : 2
options zfs l2arc_headroom=12</pre>

#####  zfs\_immediate\_write\_sz || zil\_slog_limit

> zfs\_log\_write() handles TX_WRITE transactions. The specified callback is called as soon as the write is on stable storage (be it via a DMU sync or a * ZIL commit).

[a useful article][7] on this tune-able parameter.  On the other hand, _zil\_slog\_limit_ is the max commit in byte to the separate log device, in short another attempt to not overuse the slog device. (ssd)

<pre># default : 32768
options zfs zfs_immediate_write_sz=131072 

# default : 1024*1024 = 1mb
options zfs zil_slog_limit=536870912</pre>

&nbsp;

These once adaptable value's have been removed :

  * zfs\_resilver\_delay
  * zfs\_scrub\_delay

**Important note : I received / found these value's floating on the internet, if you did not yet realize I am not an expert at this, this is my "best" attempt to understand these value's and settings, and this information might just be plain wrong (or dated when you read it), do your research before changing value's in production. You can't blame me if your servers turns out to be the next skynet.**

New source :

  * <https://github.com/zfsonlinux/zfs/wiki/ZFS-on-Linux-Module-Parameters>

 [1]: https://docs.oracle.com/cd/E26502_01/html/E29022/chapterzfs-3.html
 [2]: https://lists.freebsd.org/pipermail/freebsd-fs/2015-August/021765.html
 [3]: https://github.com/zfsonlinux/zfs/blob/02730c333c4fce8c0ead1f7f3404caca5cdb3936/module/zfs/vdev_queue.c
 [4]: https://forums.freebsd.org/threads/35663/
 [5]: https://en.wikipedia.org/wiki/Noop_scheduler
 [6]: https://en.wikipedia.org/wiki/Deadline_scheduler
 [7]: https://utcc.utoronto.ca/~cks/space/blog/solaris/ZFSWritesAndZIL