---
title: 'zfs: disagrees about version'
author: svennd

date: 2015-12-09T09:44:16+00:00
url: /zfs-disagrees-about-version/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS

---
[ZFSonLinux][1] (ZOL) is a great project that creates a Linux kernel port of the [ZFS][2] filesystem. However when the kernel updates, it always makes problems with ZFS kernel module 🙁  I have not found a stable solution, only a very dirty "windows alike method". I will share it as a future reference for my colleagues and -primary- myself.

<code class="EnlighterJSRAW" data-enlighter-language="null">Failed to load ZFS module stack.</code>

In essence, a new kernel is installed, it will "weak" link the ZFS modules, for some reason ZFS doesn't like that and gets partially updated. Both the new and the old kernel will not be able to load the ZFS data, for people who are now in full panic mode (like myself every-time this happens) : **Your data is not lost. **

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># find the version of spl and zfs
dkms status

# both remove them
dkms remove -m zfs -v 0.6.3 --all
dkms remove -m spl -v 0.6.3 --all

# install the headers for the new kernel
# ubuntu/debian
apt-get install linux-headers-$(uname -r)

# centos
yum install kernel-headers

# reinstall zfs
yum reinstall zfs

#add & build them again
dkms add -m spl -v 0.6.3
dkms add -m zfs -v 0.6.3
dkms install -m spl -v 0.6.3
dkms install -m zfs -v 0.6.3

# try loading in :
modprobe zfs

# if you can load zfs again now, you can skip this step
# I can't so I had to reboot my machine. (I know its crazy)
# find the data 
zpool import

# my poolname is tank
zpool import tank
</pre>

And that is how I saved myself! (for now.)

During upgrade from 0.8.x -> 2.x I found files here being annoying :

<pre>/var/lib/dkms/zfs
</pre>

&nbsp;

Some notes :

  * Reinstalling doesn't always work, sometimes you just need to remove zfs <code class="EnlighterJSRAW" data-enlighter-language="shell">yum remove zfs</code> after that its a good idea to clean up dkms manually. The command below is floating around on the web; It comes down to removing the modules from <code class="EnlighterJSRAW" data-enlighter-language="null">/lib/modules/$(kernel_version)/extra/</code>  I removed them from all the kernels, as I only wanted to use the newest kernel anyway. <pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">find /lib/modules/$(uname -r)/extra -name "splat.ko" -or -name "zcommon.ko" -or -name "zpios.ko" -or -name "spl.ko" -or -name "zavl.ko" -or -name "zfs.ko" -or -name "znvpair.ko" -or -name "zunicode.ko" | xargs rm -f
find /lib/modules/$(uname -r)/weak-updates -name "splat.ko" -or -name "zcommon.ko" -or -name "zpios.ko" -or -name "spl.ko" -or -name "zavl.ko" -or -name "zfs.ko" -or -name "znvpair.ko" -or -name "zunicode.ko" | xargs rm -f</pre>

  * Update 11/01/2016 
      * The same problem happened today, a Centos 6.X server crashed due to a raidcontoller blocking. This forced a reboot, for some reason this booted in the not-latest-installed kernel, so zfs was installed in a newer kernel and weak linked to the "older" kernel. Rebooting in this case is the thing you should try first.
  * Update  14/12/2016 
      * Its nice to see more people facing the same issues, here is a similar solution [to solve it on CentOS 7][3]

 [1]: http://zfsonlinux.org
 [2]: https://en.wikipedia.org/wiki/ZFS#ZFS_and_hardware_RAID
 [3]: https://icesquare.com/wordpress/centosrhel-7-no-zfs-after-updating-the-kernel/