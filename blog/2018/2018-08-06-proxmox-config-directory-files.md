---
title: 'Proxmox config directory & files'
author: svennd

date: 2018-08-06T08:19:28+00:00
url: /proxmox-config-directory-files/
thumbnail: /img/2020/02/nightsky.jpg
categories:
  - proxmox
  - SysAdmin
tags:
  - proxmox

---
A small list for important Proxmox files & directories.

For LXC (Linux containers) :

<pre>config: /var/lib/lxc/$ct_id/config</pre>

For KVM (Kernel-based Virtual Machine, mostly Windows machines)

<pre>config: /etc/pve/qemu-server/$kvm_id.conf</pre>

Cluster files (if you use Proxmox cluster)

<pre>per node configuration : /etc/pve/nodes/$node_name/*</pre>

Location of the ISO files that can then be loaded from the web interface :

<pre>/var/lib/vz/template/iso/</pre>