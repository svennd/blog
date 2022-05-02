---
title: Installing Gluster, 3 brick distributed, Centos 7
author: svennd

date: 2019-02-08T19:41:34+00:00
url: /installing-gluster-3-brick-distributed-centos-7/
thumbnail: /img/2020/05/katie-rodriguez-LbRpElYxzPc-unsplash-scaled.jpg
classic-editor-remember:
  - classic-editor
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - gluster

---
<pre>yum install centos-release-gluster
yum install glusterfs-server</pre>

Verify

<pre>[root@303 ~]# glusterfs --version
glusterfs 3.12.9
Repository revision: git://git.gluster.org/glusterfs.git
Copyright (c) 2006-2016 Red Hat, Inc. &lt;https://www.gluster.org/&gt;
GlusterFS comes with ABSOLUTELY NO WARRANTY.
It is licensed to you under your choice of the GNU Lesser
General Public License, version 3 or any later version (LGPLv3
or later), or the GNU General Public License, version 2 (GPLv2),
in all cases as published by the Free Software Foundation.
</pre>

start service

<pre>service glusterd start
service rpcbind start
</pre>

probe node

<pre>gluster peer probe gluster3
gluster peer probe gluster2

[root@gluster2 ~]# service glusterd start
Redirecting to /bin/systemctl start glusterd.service
[root@gluster2 ~]# gluster peer probe gluster3
peer probe: success.
[root@gluster2 ~]# gluster peer probe gluster2
peer probe: success. Probe on localhost not needed
</pre>

create /data, create volume

<pre>[root@303 ~]# mkdir /data
[root@303 ~]# gluster volume create triple gluster1:/data gluster2:/data gluster3:/data
volume create: triple: failed: The brick gluster3:/data is being created in the root partition. It is recommended that you don't use the system's root partition for storage backend. Or use 'force' at the end of the command if you want to override this behavior.
[root@303 ~]# gluster volume create triple gluster1:/data gluster2:/data gluster3:/data force
volume create: triple: success: please start the volume to access data</pre>

verify

<pre># gluster volume info

Volume Name: triple
Type: Distribute
Volume ID: 34767df2-24e3-438f-be5d-53edeaefef4f
Status: Created
Snapshot Count: 0
Number of Bricks: 3
Transport-type: tcp
Bricks:
Brick1: gluster1:/data
Brick2: gluster2:/data
Brick3: gluster3:/data
Options Reconfigured:
transport.address-family: inet
nfs.disable: on
</pre>

start volume

<pre>[root@303 ~]# gluster volume start triple
volume start: triple: success
[root@303 ~]# gluster volume info

Volume Name: triple
Type: Distribute
Volume ID: 34767df2-24e3-438f-be5d-53edeaefef4f
Status: Started
Snapshot Count: 0
Number of Bricks: 3
Transport-type: tcp
Bricks:
Brick1: gluster1:/data
Brick2: gluster2:/data
Brick3: gluster3:/data
Options Reconfigured:
transport.address-family: inet
nfs.disable: on
</pre>

check status

<pre>[root@303 ~]# gluster volume status
Status of volume: triple
Gluster process                             TCP Port  RDMA Port  Online  Pid
------------------------------------------------------------------------------
Brick gluster1:/data                        49152     0          Y       387
Brick gluster2:/data                        49152     0          Y       1297
Brick gluster3:/data                        49152     0          Y       1216

Task Status of Volume triple
------------------------------------------------------------------------------
There are no active volume tasks
</pre>

&nbsp;

&nbsp;