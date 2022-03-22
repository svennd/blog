---
title: Snapshots in ZFS (ZOL)
author: svennd

date: 2017-01-22T19:28:57+00:00
url: /snapshots-in-zfs-zol/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - zfs

---
One of the impressive tools in ZOL (ZFS On Linux) is snapshots. Snapshots are a way to make a 'instant backup' of the files on the system. The name backup, is not completely correct. If the system dies, the snapshots won't save you, since snapshots are on the same storage as the data, they don't eat any unneeded extra space.

A snapshot is the state of the pool/dataset when you created the snapshot. In ZFS snapshots are cheap and fast, so its smart to use the feature to your advantige. The snapshot will remember how the files looked when you create the snapshot, and will only require extra space when changes to those files are made.

#### **How to use**

**Creating** a snapshot :

<pre>zfs snapshot pool@anythingyouwant
zfs snapshot pool/dataset@anythingyouwant</pre>

**Destroy** a snapshot :

<pre>zfs destroy pool@snapname
zfs destroy pool/dataset@snapname</pre>

**Rename **a snapshot :

<pre>zfs rename pool/dataset@name pool/dataset@new_name</pre>

**List **snapshots :

<pre>zfs list -t snapshot 
zfs list -t snapshot -r pool/dataset</pre>

**Rollback **from a snapshot :

<pre>zfs rollback pool/dataset@name</pre>

_note : take care when doing a rollback._

#### **Demo**

Lets see this in a simple example. I first created a _demo_ filesystem.__  
__ 

<pre>zfs create tank/demo</pre>

Lets create a first snapshot on this empty system :

<pre>zfs snapshot tank/demo@initial</pre>

Getting a list of the current snapshots : _zfs list -t snapshot _There is an option to show snapshots on _zfs list _but thats makes it even more cloudy to work.

<pre># zfs list -t snapshot
NAME                                                USED  AVAIL  REFER  MOUNTPOINT
tank/demo@initial                                      0      -  35.9K  -</pre>

Now to generate some data, in ZFS (most COW FS) [_fallocate _][1]won't work. So I use a fancy trick with _head_ but in essence this just generates a file of approx. 10M.

<pre>head -c 10M &lt;/dev/urandom &gt;data
head -c 10M &lt;/dev/urandom &gt;data2
head -c 10M &lt;/dev/urandom &gt;data3</pre>

Nothing special going on here :

<pre># ls -lh
total 31M
-rw-r--r-- 1 root root 10M Jan 22 19:07 data
-rw-r--r-- 1 root root 10M Jan 22 19:07 data2
-rw-r--r-- 1 root root 10M Jan 22 19:07 data3

# zfs list
NAME                     USED  AVAIL  REFER  MOUNTPOINT
tank                    9.96T  11.1T  3.48G  /tank
tank/demo               30.1M  11.1T  30.0M  /tank/demo</pre>

As expected, when you see _zfs list_ there is about 30M in use. Now since this is data that was not in our original (empty) set. The snapshot should not store any changes.

<pre># zfs list -t snapshot
NAME                                                USED  AVAIL  REFER  MOUNTPOINT
tank/demo@initial                                  20.2K      -  35.9K  -</pre>

_note : the 20.2k are meta-data changes._

Now its time to take a snapshot on the important data we just generated :

<pre># zfs snapshot tank/demo@today

# zfs list -t snapshot
NAME USED AVAIL REFER MOUNTPOINT
tank/demo@initial 20.2K - 35.9K -
tank/demo@today 0 - 30.0M -</pre>

Now lets try snapshots, I did a move, a deletion, a overwrite (update) and a new file creation.

<pre>mv data data_moved
rm -rf data2
cp data_moved data3
head -c 10M &lt;/dev/urandom &gt;data4


# zfs list -t snapshot
NAME USED AVAIL REFER MOUNTPOINT
tank/demo@initial 20.2K - 35.9K -
tank/demo@today 20.0M - 30.0M -</pre>

Exactly what we expected, the snapshot took a "backup" of data2 and data3 which where removed and overwritten, since data1 was not changed or removed, it does not occupy extra space and data4 is a new file, so it was not there during creation of the snapshot.

Now how do we get the data back ? There are two options, first for small issue's like accidental removals, you can use the hidden _.zfs_ directory, for larger issue's you can mount the snapshot on a seperate folder. In every directory on ZFS there is a hidden folder _.zfs. _Note that even _ls -la_ will not pick that directory up.

<pre># ls -ls .zfs/snapshot/
total 1
0 dr-xr-xr-x 1 root root 0 Jan 22 19:25 initial
1 drwxr-xr-x 2 root root 5 Jan 22 19:07 today

# ls -ls .zfs/snapshot/today/
total 30729
10243 -rw-r--r-- 1 root root 10485760 Jan 22 19:07 data
10243 -rw-r--r-- 1 root root 10485760 Jan 22 19:07 data2
10243 -rw-r--r-- 1 root root 10485760 Jan 22 19:07 data3</pre>

Voila, there are our data files, prior to removal/overwrite/rename. Note that with this method the snapshot is auto-mounted to _/tank/demo/.zfs/snapshot/today_ as you might have guessed, you can also manually mount snapshots to a location. This can be done in the 'normal' Linux way :

<pre># mkdir today_snapshot
# mount -t zfs tank/demo@today today_snapshot

# ls -l today_snapshot/
total 30729
-rw-r--r-- 1 root root 10485760 Jan 22 19:07 data
-rw-r--r-- 1 root root 10485760 Jan 22 19:07 data2
-rw-r--r-- 1 root root 10485760 Jan 22 19:07 data3

</pre>

To unmount just use _umount_

<pre>umount tank/demo@today</pre>

Our snapshot name was not chosen very long-term minded, you can rename them to something more verbose :

<pre>zfs rename tank/demo@today tank/demo@22jan2017</pre>

The last thing we want to do, is remove a snapshot, lets remove the initial snapshot, since there was no data :

<pre>zfs destroy tank/demo@initial</pre>

There is another option, which I don't trust much beside this example and I would only advice it to use with extreme care. Rollback, can be done straight back to a state of the system at the time of the snapshot creation, however only back to the latest snapshot. If you want to go back further, you need to destroy the snapshots in between.

<pre>zfs rollback -r tank/demo@22jan2017</pre>

_Note the -r will destroy any snapshots between current state and @22jan2017 snapshot._

#### **Relevant**

  * Snapshots are so cheap, people tend to forget to make them. However, cheap is not free, so making a cron to generate a snapshot every month/week/day/hour will eventually put your tank [full with snapshots][2]. Therefor I suggest using a snapshot systems that creates and prunes snapshots. I chose [sanoid][3], a good system, [this article can help you install sanoid.][4]
  * With multiple datasets, and each having multiple snapshots, its sometimes difficult to get a good overview of howmany snapshots each dataset has. You can use this shell cmd to check : <pre>zfs list -t snapshot | tail -n +2 | sed 's/@/\t/g' | cut -f 1 | uniq -c</pre>
    
    Which generates something like :
    
    <pre># zfs list -t snapshot | tail -n +2 | sed 's/@/\t/g' | cut -f 1 | uniq -c
     30 huginn/complgen3
      2 jbod1/breva
      1 jbod1/users
      5 jbod1/users/flylab
      5 jbod1/users/proteo
      2 jbod1/users/servers
      5 jbod1/users/userA</pre>
    
    &nbsp;</li> </ul> 
    
    &nbsp;

 [1]: https://github.com/zfsonlinux/zfs/issues/326
 [2]: https://www.svennd.be/full-zfs-filesystem-no-space-left-on-device/
 [3]: https://github.com/jimsalterjrs/sanoid
 [4]: https://www.svennd.be/zfs-snapshots-of-proxmox-using-sanoid/