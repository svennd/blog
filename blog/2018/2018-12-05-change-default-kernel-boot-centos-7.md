---
title: Change default kernel boot Centos 7
author: svennd

date: 2018-12-05T13:18:18+00:00
url: /change-default-kernel-boot-centos-7/
thumbnail: /img/2020/05/dylan-alcock-R4ZiMTvAQbA-unsplash-scaled.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - kernel

---
I installed a new kernel (kernel-ml, from **m**ain**l**ine 4.18) to make sure on next boot it got selected this is what I had to do : (Since its headless and I won't see grub when it boots)

Install kernel mainline :

<pre>yum install kernel-ml</pre>

Find the current installed kernels :

<pre>cat /boot/grub2/grub.cfg | grep ^menuentry | cut -c1-50 | nl -v 0</pre>

for me :

<pre>0  menuentry 'CentOS Linux (4.18.16-1.el7.elrepo.x86_
1  menuentry 'CentOS Linux (3.10.0-693.5.2.el7.x86_64
2  menuentry 'CentOS Linux (0-rescue-daa04fd470a24a8f</pre>

So I want the first kernel (starting from 0) as default; This can be added in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/default/grub</code> :

<pre>GRUB_DEFAULT=0</pre>

Then remake grub config :

<pre>grub2-mkconfig -o /boot/grub2/grub.cfg</pre>

Reboot and the new kernel is :

<pre>[root@svennd ~]# uname -a
Linux svennd.local 4.18.16-1.el7.elrepo.x86_64 #1 SMP Sat Oct 20 12:52:50 EDT 2018 x86_64 x86_64 x86_64 GNU/Linux
</pre>

&nbsp;

&nbsp;
