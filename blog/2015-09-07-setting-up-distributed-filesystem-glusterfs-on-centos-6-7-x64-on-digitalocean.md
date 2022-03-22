---
title: Setting up GlusterFS on CentOS 6.7 x64 on DigitalOcean
author: svennd

date: 2015-09-07T15:43:09+00:00
url: /setting-up-distributed-filesystem-glusterfs-on-centos-6-7-x64-on-digitalocean/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - glusterfs

---
There are already allot of tutorials on the _digitalocean_ website, including one on [glusterFS on Ubuntu][1]. I however wanted to test with CentOS before deploying GlusterFS local, while the method described by [glusterfs][2] themself refers to CentOS & RHEL variants, it actually doesn't work. This is a fresh two node installation from start till working replication on CentOS 6.7 64bit.

Like all virtual machines, I start off with a clean slate and an up-to-date machine.

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># do updates
# install yum-cron (auto updater) and nano (editor)
yum update -y && yum install yum-cron nano -y

# set timezone
 ln -sf /usr/share/zoneinfo/Europe/Brussels /etc/localtime</pre>

After that you need to add the repo data of glusterfs, as the installed repo's don't contain glusterfs-server (they do contain other glusterfs library's but that one is missing.)

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># add as /etc/yum.repo.d/gluster.repo

[glusterfs-epel]
name=GlusterFS is a clustered file-system capable of scaling to several petabytes.
baseurl=http://download.gluster.org/pub/gluster/glusterfs/3.7/LATEST/EPEL.repo/epel-$releasever/$basearch/
enabled=1
skip_if_unavailable=1
gpgcheck=1
gpgkey=http://download.gluster.org/pub/gluster/glusterfs/3.7/LATEST/EPEL.repo/pub.key

[glusterfs-noarch-epel]
name=GlusterFS is a clustered file-system capable of scaling to several petabytes.
baseurl=http://download.gluster.org/pub/gluster/glusterfs/3.7/LATEST/EPEL.repo/epel-$releasever/noarch
enabled=1
skip_if_unavailable=1
gpgcheck=1
gpgkey=http://download.gluster.org/pub/gluster/glusterfs/3.7/LATEST/EPEL.repo/pub.key

[glusterfs-source-epel]
name=GlusterFS is a clustered file-system capable of scaling to several petabytes. - Source
baseurl=http://download.gluster.org/pub/gluster/glusterfs/3.7/LATEST/EPEL.repo/epel-$releasever/SRPMS
enabled=0
skip_if_unavailable=1
gpgcheck=1
gpgkey=http://download.gluster.org/pub/gluster/glusterfs/3.7/LATEST/EPEL.repo/pub.key
</pre>

[source][3]

After that you can start installing.

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># you need epel, this should install the correct one
yum install epel-release

# since we need to load the data first update
# your local cache, then install gluster-server
# this will include all the other dependencies but 
# will take the latest version from the repo we just added (glusterfs repo)
yum clean all && yum update && yum install glusterfs-server

# start rpcbind & glusterfs on boot
chkconfig rpcbind on
chkconfig glusterd on

# start the services now
/etc/init.d/glusterd start
/etc/init.d/rpcbind start</pre>

_notice : failing to start rpcbind, will cause NFS not being able to load. The result is that everything work until you mount NFS and start writing files in it. (they don't get replicated) The error you would get is : (cat /var/log/glusterfs/nfs.log)_

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">0-/usr/sbin/glusterfs: Started running 
0-epoll: Started thread with index 1
0-rpc-service: Configured rpc.outstanding-rpc-limit with value 16
0-nfs-mount: Exports auth has been disabled!
0-rpc-service: Could not register with portmap 100005 3 38465
0-nfs: Required program  MOUNT3 registration failed
0-nfs: Failed to initialize protocols
0-nfs-server: Initialization of volume 'nfs-server' failed, review your volfile again
0-nfs-server: initializing translator failed
0-graph: init failed</pre>

Next I added the both machines to each /etc/hosts (as this is a test setup and I don't want to wait for DNS update)

first_machine@/etc/hosts

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">127.0.0.1 first_machine first_machine
123.123.123.123 second_machine second_machine</pre>

second_machine@/etc/hosts

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">127.0.0.1 second_machine second_machine
123.123.123.123 first_machine first_machine</pre>

&nbsp;

The 127.0.0.1 is needed as I used the names of the machines and otherwise you get a _gluster peer probe: failed: Probe returned with unknown errno 107 ([also possible](http://www.boxtricks.com/gluster-peer-probe-failed-probe-returned-with-unknown-errno-107/" target="_blank))._

After that its pretty much following the tutorial : (from second_machine)

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># connect them to the new trusted pool
gluster peer probe first_machine

# create local directory (if you use disks this would be the mountpoint)
mkdir -p /data/my_gluster_share

# create a replication volume of 2 bricks
# this also created /data/my_guster_share on the first_machine (make sure its empty!)
gluster volume create gv0 replica 2 first_machine:/data/my_gluster_share second_machine:/data/my_gluster_share

# start the volume
gluster volume start gv0

# check status (all columns should be Y, if not something is not working!)
gluster volume status</pre>

Hope this helps someone, up to our next adventure!

 [1]: https://www.digitalocean.com/community/tutorials/how-to-create-a-redundant-storage-pool-using-glusterfs-on-ubuntu-servers
 [2]: http://gluster.readthedocs.org/en/latest/Quick-Start-Guide/Quickstart/
 [3]: http://download.gluster.org/pub/gluster/glusterfs/LATEST/EPEL.repo/glusterfs-epel.repo