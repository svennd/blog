---
title: Sharenfs on ZFS and mounting with autofs
author: svennd

date: 2020-03-25T13:07:13+00:00
url: /sharenfs-on-zfs-and-mounting-with-autofs/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - autofs
  - zfs

---
ZFS has this less documented feature, called share[nfs|smb]; I tried it once, it "did not work on first attempt"™ So I ignored it; However now we faced an issue where we normally exported ZFS volumes using /etc/exports (NFS); and mounted using /etc/fstab; but got an empty directory where there was a "sub" zpool volume; This seems counter intuitive as on the NFS exporting system you don't see any difference in child pools directory structure;

For example : (on NFS server)

<pre>[root@tinky /]# zfs list
NAME                USED  AVAIL     REFER  MOUNTPOINT
tinky              31.4T  49.0T      256K  /tinky
tinky/archive       219K  49.0T      219K  /tinky/archive
tinky/upload        219K  49.0T      219K  /tinky/upload
</pre>

In order to export the full /tinky directory; we should do the following in /etc/exports :

<pre>/tinky 127.168.0.0/16(rw,async,no_subtree_check,no_root_squash) 
</pre>

This we can mount; and should give :

<pre>/tinky
/tinky/archive
/tinky/upload</pre>

Which it does, yaaay ! **Wrong !** All the data stored under /tinky/archive and /tinky/upload is invisible on the mount ! In fact these directories are empty.

Since zpool children are really easy (unlike human children) it would be really annoying to have to create a /etc/export and /etc/fstab entry for each of those childeren; There is a better way; using _sharenfs_ option in ZFS and _autofs_ (which I played around with already before, [here][1]).

In order to start the share with ZFS simply do :

<pre>zfs sharenfs=on tinky</pre>

You can check the result :

<pre>[root@tinky /]# zfs get all | grep nfs
tinky                 sharenfs              on                     local
tinky/archive         sharenfs              on                     inherited from tinky
tinky/upload          sharenfs              on                     inherited from tinky
</pre>

and/or

<pre>[root@tinky ~]# showmount -e
Export list for tinky:
/tinky             *
/tinky/archive     *
/tinky/upload      *</pre>

If you want to restrict the shares or adapt the options; you can use :

<pre>zfs set sharenfs='rw=@192.0.10.0/16,rw=@192.0.11.0/24' tinky</pre>

in this case I restrict to two IP ranges; but go crazy. On issuing this command, they are automatically exported; which you can verify using _exportfs_

<pre>[root@tinky /]# exportfs
/tinky/upload   192.168.20.0/24
/tinky/archive  192.168.20.0/24
</pre>

_note : reduced output._

In case you don't see it, you can try to share manually :

<pre>zfs share -a</pre>

and surprising, if you wish to un-share :

<pre>zfs unshare -a</pre>

On the remote machine, you can verify if you have access to the NFS mounts using showmount -e , using the IP of the NFS server;

<pre># showmount -e 192.168.20.208
Export list for 192.168.20.208:
/tinky             192.168.20.0/24
/tinky/archive     192.168.20.0/24
/tinky/upload      192.168.20.0/24
</pre>

<del>The auto-mount itself now seems smarter then before; although I'm unsure exactly how, simply adding the root of the share is enough to see all the subvolumes in ZFS :</del>

<pre>tinky       -rw,hard,intr,rsize=32768,wsize=32768,nfsvers=3\
        /tinky                  192.168.20.208:/tinky</pre>

Happy ZFS sharing 😉

&nbsp;

// update

Turns out the issue is still there with this method;

// update 2  
might be required :

<pre>systemctl nfs-server enable
systemctl nfs-server start
</pre>

 [1]: https://www.svennd.be/autofs_rocks_nfs/