---
title: 'ZFS : Unknown parameter `zil_slog_limit’'
author: svennd

date: 2018-06-01T19:52:44+00:00
url: /zfs-unknown-parameter-zil_slog_limit/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - zfs

---
![sleep and sysadmin](/img/2018/06/2bfx0g.jpg)

This is the tale of caution, when sleep deprived, sysadmin with caution! Let's be honest who never said : lets finish this game, let's have another drink, I will sleep when dead, ... (enter sleepless night excuse here) ? Last night for me I was on a coding spree, when a thunderstorm broke, I was like, whoa its already too late, let's take my camera and try and take some thunder shots. (it failed) It ended up being a very short 5h night.

Fast forward to the next morning. There was a planned power-down at work but UPS's (**U**ninterruptible **P**ower **S**upply) should take the heat if not extended due to circumstances. We had prepared for this situation two weeks prior, when it was cancelled. So I was pretty sure my task would be to sit there and say, good work team!

It was a 7 minute blackout, butter smooth, we planned for 15m ! After the planned boot procedure all was up and running in no time. With exception of one machine, that had updated and was now running Centos 7.5. The machine refused to show the ZFS partitions, while normally this machine is playing nice, it was not today. I immediately thought of [kernel module not loading][1] however that failed ! Time to panic ! Well for sure data was not gone, since its just not accessible in this kernel. So the panic was never really there, as my brain was to slow to really get in the panic mood. Just add coffee and time I thought.

My first attempt was to remove _zfs_ and _spl _modules and redo the [dkms installation][2] following the official guide, after removing every trace of ZFS, that royally failed. Ok. Time for kABI. Which until today I assumed was kernel-my-girlfriends-name (Abby) it turns out its not and its actually Kernel Application Binary Interface. As the red-hat customer platform says nicely :

> kABI is a set of in-kernel symbols used by drivers and other kernel modules. Each major and minor Red Hat Enterprise Linux release (_and Centos/Fedora kernel)_ has set of in-kernel symbols whitelist, which are defined in "/usr/src/kernels/"kernel-version"/Module.symvers" file. [source][3]

Following the [guide for kABI,][4] succes was not to be found. Ok, I got no errors that yielded any google results, so it was time for the deep and dirty. Let's compile from source ! Failed. I had allot of kernels installed, so let's remove them all except one and rebuild. Fail. Maybe the kernel is too new, let's install an older kernel and rebuild again. Fail. Good, this 0.7.x version of ZFS is at fault, let's try this 0.5.X. Fail. Let's check Centos board to see if there are known issues. Nothing. Let's check the Github of the ZoL project. Nothing.

Good I genuinely found a new bug in ZFS with working in Centos 7.5. (by this time, my brain should have said, wait a minute, did we read the error ?) Even tho, Centos 7.5 is out for a while already and even before that RHEL 7.5 is always out first to the enterprise users.... So this error makes no sense what so ever.

As my brain was pretty stable, focused and definitely not counting the minutes till weekend. It also had enough sleep and no any kind of substances such as "OD levels" of caffeine and sugar. I made the choice to claim ZFS developers time and [open a new ticket][5] with the developers. After all they fubar'd right ?

The only thing I did not do was to really read the error :

<pre>zfs: Unknown parameter 'zil_slog_limit'</pre>

Yes, if you set tuning value's for a module and the module updates significantly (0.5 -> 0.7) some parameters get dumped/deprecated. I feel a bit ashamed for stealing developer time for my own stupidity. But I hope this blog post is a bit of payback. Next person who google's this error will find my shame post and not waste dev. time.

As to the fix; comment or remove <code class="EnlighterJSRAW" data-enlighter-language="null">zil_slog_limit</code> in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/modprobe.d/zfs.conf</code> or similar configuration file.

Thanks [behlendorf][6], I hope you get a good's night rest !

 [1]: https://www.svennd.be/zfs-disagrees-about-version/
 [2]: https://github.com/zfsonlinux/zfs/wiki/RHEL-and-CentOS#rhelcentos-7x-kmod-package-upgrade
 [3]: https://access.redhat.com/solutions/444773
 [4]: https://github.com/zfsonlinux/zfs/wiki/RHEL-and-CentOS#kabi-tracking-kmod
 [5]: https://github.com/zfsonlinux/zfs/issues/7590
 [6]: https://github.com/behlendorf