---
title: Basic ZFS tune tips
author: svennd

date: 2017-01-26T15:31:56+00:00
url: /basic-zfs-tune-tips/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - SysAdmin
  - ZFS
tags:
  - zfs

---
Lets start out with saying that ZFS on Linux (ZoL) is pretty awesome, [snapshots][1], [compression][2]-on-the-fly, quota's, ... I love it. However ZoL also has issue's, I even blogged about some in the past. ([a full ZFS pool][3], [failing to load ZFS module][4]) Most cases where my own fault, but still, its very uncommon for a file system to be such a pain in the ass.

The last few days, I tried to find out why, my installation was so slow. Slow in this context is hard to define. Sometimes users complained about slow _ls_ runs, while write speed was decent enough ... read speed was also fine, perhaps NFS was at fault, perhaps disks, controller, ... ?

Long story short, I looked for some tuning options, as the defaults most likely aren't perfect. Before I take all the credit, I was masterly helped by senior systems engineer, [ewwhite][5] in this matter. So lets go over some "simple" tune options during initial creation.

#### **The setup**

  * Centos 7.3 (up-to-date, nightly)
  * 128GB RAM
  * Xeon E5-2620 v3
  * 3 * raidz2 with each 12 x 4Tb drives SAS 3 (HGST Ultrastar 7K6000)
  * 2 * Intel SSD DC P3600 Series PCI-E, 400GB (used for cache and in mirror logs, partitioned)
  * ZFS on Linux : v0.6.5.8

Bragging rights, right ? Sadly its for work... (or happy for my power bill)

#### **Tuning during creation**

Setting these value's can be done on a live system, however most of them will only apply for new data or rewrites, so try to do them when creating a pool. Setting them to a pool is done :

<pre># in case you want to apply to a specific subpool
zfs set parameter=value pool/subpool

# pool and all not locally set subpools, will receive this parameter
zfs set parameter=value pool</pre>

Sub-pools inherit from the pool above them, so setting these features will auto-magically inherit from the top level setting when you create sub-pools. (Eg. _tank/users_ will inherit from _tank_ unless defined otherwise)

  * _xattr=sa_, default _xattr=on_ 
      * What happens on default behavior is that for every _xattr_ object a file in a hidden directory is created, so browsing or _ls_'ing requires up to three disk seeks, on top of that it seems its not really well cached. The reason it was not changed to default behavior seems to be compatibility, which is a good reason if you work with other systems such as Solaris. But for Linux only environment its not relevant.
      * This option only works for newly written files, so if you only notice this tune option post-creation, you will need to remove and copy them in the pool again to take effect. Perhaps you can use _zfs send_ and _zfs receive_. First set the _xattr=sa_ then _zfs send_ to an image, destroy the location, and _zfs receive_ in the (old) location. (note : Not sure if this works, you could also use rsync here, which most likely will work.)
      * [original implementation][6], [I'm not alone award][7].
      * <pre>zfs set xattr=sa tank/users
zfs send tank/users &gt; tank/users_disk.img
zfs destroy tank/users
zfs receive tank/users &lt; tank/users_disk.img</pre>

  * _acltype=posixacl_, default _acltype=off_ 
      * The reason behind this is fuzzy to me, but they parameters come up together regularly, so they are most likely related somehow. (need to research further)

  * _compression=lz4_, default_ compression=off_ 
      * I did activate this option when I first started, basically the file system will try and compress all files, but when an application requests them, it will decompress them, without anyone noticing, of-course this creates a bit of CPU but any decent server should be able to do it. On a ~100 TB system : <pre>[root@huginn jbod1]# zfs get all huginn| grep compres
huginn  compressratio         1.36x                  -
huginn  compression           lz4                    local</pre>
        
        That's 36 TB of extra space... note : allot of data is already compressed more heavy using _gzip_. This is a must have. Not only does it help you get more storage, writing uncompressed data is most likely taking longer then writing compressed data.</li> </ul> </li> 
        
          * atime=off 
              * _atime_ is short for "last" access time, so every time a file is read a update has to flush to disk to reflect this access item. For most applications that's totally irrelevant and a quit overhead.  I always set it to off. In cases where you wish to use access time, look to _relatime_.
          * relatime=off 
              * _relatime_ was introduced in Linux 2.6.20 kernel. It only updates the _atime_ when the previous _atime_ is older then _mtime_ (modification time) or _ctime_ (changed time) **or** when the _atime_ is older then 24h (based on setting). I am assuming the zfs implementation is using this kernel feature. I don't require _relatime_ or _atime_, but this is the preferred option. Perhaps in the future we might see _lazytime_ (linux kernel 4.X) which uses memory to store _atime_ updates and write them during normal I/O.</ul> 
        
        And that's it, next time lets look at the kernel module 🙂
        
        ####

 [1]: https://www.svennd.be/snapshots-in-zfs-zol
 [2]: https://www.svennd.be/zfs-compression-use-it/
 [3]: https://www.svennd.be/full-zfs-filesystem-no-space-left-on-device/
 [4]: https://www.svennd.be/zfs-disagrees-about-version/
 [5]: http://serverfault.com/users/13325/ewwhite
 [6]: https://github.com/zfsonlinux/zfs/issues/443
 [7]: https://www.reddit.com/r/zfs/comments/44dm4l/setting_xattrsa_after_the_fact/