---
title: Mount NFS on LXC Proxmox
author: svennd

date: 2018-08-08T20:12:33+00:00
url: /mount-nfs-lxc-proxmox/
thumbnail: /img/2020/02/nightsky.png
categories:
  - Linux
  - proxmox
  - SysAdmin
tags:
  - proxmox

---
I'm a long time user of Proxmox (a few years), and recently I had the chance to upgrade an by-now ancient Proxmox 3.4 to current 5.2. In that time frame the developers have changed from OpenVZ to LXC and made a script to migrate the data. One key element however, mounting (remote) NFS shares are no longer possible from within the containers, at least not native.

Within the container the error is rather lacking information and is pointing towards the NFS server issue.

<pre>Aug  8 09:09:51 svennd mount: mount.nfs: access denied by server while mounting nfs_server:/data
</pre>

However, on the Proxmox host, in /var/log/messages you can find that _apparmor_ is the problem.

<pre>apparmor="DENIED" operation="mount" info="failed flags match" error=-13 profile="lxc-container-default-cgns" name="/storage/nfs_server/data/" pid=25086 comm="mount.nfs" fstype="nfs" srcname="nfs_server:/data" flags="ro, noatime"</pre>

:thought_balloon:.

Well now, lets try and undo this security feature, in my case the profile that is causing it to block is _lxc-container-default-cgns_. You can find this file : <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/apparmor.d/lxc/lxc-default-cgns</code> Also some other configs can be found there (not sure when what profile is loaded) I added :

<pre>mount fstype=rpc_pipefs,
mount fstype=nfs,</pre>

below <code class="EnlighterJSRAW" data-enlighter-language="null">mount fstype=cgroup -&gt; /sys/fs/cgroup/**,</code> resulting in this final file :

<pre># Do not load this file.  Rather, load /etc/apparmor.d/lxc-containers, which
# will source all profiles under /etc/apparmor.d/lxc

profile lxc-container-default-cgns flags=(attach_disconnected,mediate_deleted) {
  #include &lt;abstractions/lxc/container-base&gt;

  # the container may never be allowed to mount devpts.  If it does, it
  # will remount the host's devpts.  We could allow it to do it with
  # the newinstance option (but, right now, we don't).
  deny mount fstype=devpts,
  mount fstype=cgroup -&gt; /sys/fs/cgroup/**,
  mount fstype=rpc_pipefs,
  mount fstype=nfs,
}</pre>

After that we need to reload Apparmor, I'm not sure what made it work again, but it was one of these :

<pre>apparmor_parser -r /etc/apparmor.d/lxc-containers
systemctl apparmor reload</pre>

And now we can mount from within once more ! 🙂

There is an alternative, but from what I [read here][2] you need to remap user ID's, and need to use mountpoints on the host and draw them inside the container.

 [1]: https://forum.proxmox.com/threads/nfs-file-system-mount-problem-apparmor.31706/
 [2]: https://pve.proxmox.com/wiki/Unprivileged_LXC_containers
