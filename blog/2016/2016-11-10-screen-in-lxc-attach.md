---
title: screen in lxc-attach || Must be connected to a terminal.
author: svennd

date: 2016-11-10T13:39:59+00:00
url: /screen-in-lxc-attach/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I tried to create a [screen][1] session on a Proxmox/lxc container, but got greeted with :

<pre># screen -S rsync_archive_docs
Cannot access '/dev/pts/3': No such file or directory</pre>

What exactly is happening I'm not sure, but it looks like I already have some sort of session since I logged on, over the host (lxc-attach -n $ID) so either log in over a new ssh session directly to the server or, if you are playing with NAT like me, you can just run :

<pre>script /dev/null</pre>

And that creates a fresh console and screen works again, jeeej for screen !

// update 14/02/2017

A new error now occurs :

<pre># screen -S test
Must be connected to a terminal.</pre>

This also can be fixed by _script /dev/null_

 [1]: https://www.svennd.be/linux-screen/