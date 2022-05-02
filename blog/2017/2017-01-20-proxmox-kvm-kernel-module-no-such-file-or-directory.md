---
title: 'Proxmox KVM kernel module: No such file or directory'
author: svennd

date: 2017-01-20T11:29:35+00:00
url: /proxmox-kvm-kernel-module-no-such-file-or-directory/
thumbnail: /img/2020/02/nightsky.jpg
categories:
  - proxmox
  - SysAdmin
tags:
  - proxmox

---
Hit by this little bugger this morning, while trying to move a virtual machine from proxmox 3.4 to 4.4.  This was the only VM on the machine, so I was surprised. (Never seen this before)

<pre>Could not access KVM kernel module: No such file or directory
failed to initialize KVM: No such file or directory</pre>

I know CPU's must support (Intel's) VT (or AMD-V) but that's mostly on by default ? Seems for this machine it wasn't. You can check your CPU using :

<pre>grep flags /proc/cpuinfo | grep --color vmx</pre>

If you have those flags, its most likely virtualization is disabled in the bios. I rebooted and changed the bios setting to 'enabled' and voila problem solved!