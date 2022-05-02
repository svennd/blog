---
title: NFS in a Centos 6 LXC Proxmox 4.1
author: svennd

date: 2016-01-28T11:27:19+00:00
url: /nfs-in-a-centos-6-lxc-proxmox-4-1/
thumbnail: /img/2020/02/nightsky.jpg
categories:
  - Linux
  - proxmox
  - SysAdmin
tags:
  - nfs
  - proxmox

---
Getting NFS to work seems a bit of a grey area for LXC ... I only [recently][1] switched part of our infrastructure over to LXC. But no NFS would be definitely a no-go.

We only work in a virtualized environment because its easy for backups and to efficiently use the computational resources on each of our server. That's the reason, security of what a container can do, is only a second to functionality. On top of that, most of these containers are not giving out a service to the outside world, the only reason they have a connection to the web is for LAN and updates. So before you use this "guide", know that I did not look into it.

**Installing NFS**

I started by updating & installing nfs common's.

<pre># updates
yum update -y

# install nfs
yum install nfs-utils nfs-utils-lib</pre>

Next I tried to start & keep them online after reboot.

<pre># mark them as start-during-boot
chkconfig rpcbind on
chkconfig nfs on 

# start the services
service rpcbind start
service nfs start</pre>

I received this error :

<pre>Starting NFS daemon: rpc.nfsd: Unable to access /proc/fs/nfsd errno 2 (No such file or directory).
Please try, as root, 'mount -t nfsd nfsd /proc/fs/nfsd' and then restart rpc.nfsd to correct the problem
</pre>

Which I believe means, that some of the [nfs kernel modules was not loaded when the container was started][2]. This was solved by installing nfs-kernel-server on the proxmox head.

<pre>apt-get install nfs-kernel-server</pre>

I also needed to add an exception to apparmor, I don't know exactly how apparmor works, but it can be overruled in the lxc configuration (<code class="EnlighterJSRAW" data-enlighter-language="null">/etc/pve/lxc/101.conf</code>) with : (add)

<pre>lxc.aa_profile: unconfined</pre>

After that I restarted the container in proxmox webgui. (cause I don't know the console commands 🙂 ) I retried and the services started. Although I found that NFS was not reporting as working :

<pre>service nfs status
rpc.svcgssd is stopped
rpc.mountd (pid 1061) is running...
nfsd dead but subsys locked</pre>

However a mount from an external machine worked.

/etc/exports from lxc container

<pre>/data *(rw,sync,no_root_squash,no_subtree_check)</pre>

and a soft mount from the client (non lxc in this test)

<pre>mount -o soft,rw lxc_ip:/data /mnt/tmp</pre>

So not 100% its save, but its working 🙂

 [1]: https://www.svennd.be/bye-openvz-hey-lxc/
 [2]: https://wiki.openvz.org/NFS_server_inside_container#nfsd_module
