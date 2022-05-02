---
title: iotop for normal users
author: svennd

date: 2016-03-25T11:21:19+00:00
url: /iotop-for-normal-users/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Iotop doesn't allow normal users to use this tool, this would be a security issue :

> Netlink error: Operation not permitted (1)
>
> The Linux kernel interfaces that iotop relies on now require root privileges  
> or the NET_ADMIN capability. This change occurred because a security issue  
> (CVE-2011-2494) was found that allows leakage of sensitive data across user  
> boundaries. If you require the ability to run iotop as a non-root user, please  
> configure sudo to allow you to run iotop as root.
>
> Please do not file bugs on iotop about this.

That's good for security, but heck users can't see if there process is IO bound ... that's crazy. Lucky you can just add the one exception without giving all the keys to the kingdom. (full sudo)

You could add a file to /etc/sudoers.d but you would need to adapt the /etc/sudoers anyway, so if you are not adding allot of users, (in my case one) just editing the file is also oké :

<pre>visudo</pre>

or if you don't wanne use vi (don't know how)

<pre>nano /etc/sudoers</pre>

just add this line :

<pre>user ALL=(ALL) NOPASSWD:/usr/sbin/iotop
</pre>

from now on, users will be able to use :

<pre>sudo iotop</pre>

but not :

<pre>sudo yum remove gcc</pre>

Now users have the ability to follow if their processes are IO bound or not ! (note : might leak information)

  <img aria-describedby="caption-attachment-994" loading="lazy" class="wp-image-994 size-full" src="/img//2015/07/25452288-1.png" width="594" height="640" srcset="/img/2015/07/25452288-1.png 594w, /img/2015/07/25452288-1-278x300.png 278w, /img/2015/07/25452288-1-1x1.png 1w" sizes="(max-width: 594px) 100vw, 594px" />
