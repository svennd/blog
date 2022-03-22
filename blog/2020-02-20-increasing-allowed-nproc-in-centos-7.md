---
title: Increasing allowed nproc in Centos 7
author: svennd

date: 2020-02-20T10:21:43+00:00
url: /increasing-allowed-nproc-in-centos-7/
thumbnail: /img/2020/04/centos.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos

---
The _nproc_ are the number of proces units a user can start; This is managed in the file : <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/security/limits.conf</code>but can be [overwritten][1] in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/security/limits.d/</code> normally in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/security/limits.d/20-nproc.conf</code> which has priority over the limits.conf file. To check how many nproc's you are using, you need to include the amount of threads, this can be seen using :

<pre>[root@server ~]# ps -lfu root | wc -l
603</pre>

The default limit for Centos 7 is 4096 for a user and unlimited for root;

<pre># Default limit for number of user's processes to prevent
# accidental fork bombs.
# See rhbz #432903 for reasoning.

*          soft    nproc     4096
root       soft    nproc     unlimited</pre>

On Centos 5 this was based on the amount of threads-max the kernel could handle; (50% of that)

<pre>[root@server ~]# sysctl -a | grep threads-max
kernel.threads-max = 1029577
</pre>

In Centos 6 the limit was 1024; and on Centos 7 the limit got increased to 4096; While its already higher, we could argue to increase it even further; As threads are relatively [cheap][2].

I increased it to a randomly selected 10000 hard and 8192 soft; Except for the root, although a limit might be useful, I'm afraid if a fork bomb happens, you need the unlimited power to stop it ... (perhaps soft ?)

<pre>cat /etc/security/limits.d/20-nproc.conf
# Default limit for number of user's processes to prevent
# accidental fork bombs.
# See rhbz #432903 for reasoning.

*          soft    nproc     8192
*          hard    nproc     10000
root       soft    nproc     unlimited
root       hard    nproc     unlimited</pre>

A similar thing can be done for open files per user; since there is no specific number (yet?) I used 30-nofile.conf You could base this on the maximum open files the system can handle <code class="EnlighterJSRAW" data-enlighter-language="null">cat /proc/sys/fs/file-max</code> but that seems a bit excessive !

<pre>cat /etc/security/limits.d/30-nofile.conf
# max cat /proc/sys/fs/file-max
# 13163513
*       soft    nofile  100000
*       hard    nofile  100001
</pre>

How to activate it ? Just open a new shell, and the limits are active; check :

<pre>[root@server ~]# ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 514788
max locked memory       (kbytes, -l) unlimited
max memory size         (kbytes, -m) unlimited
open files                      (-n) 100000
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 20001
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
</pre>

Useful links :

  * [all ulimit options][3]

 [1]: http://www.linuxtechnotes.com/2017/02/does-limitsd-file-overwrites-to.html
 [2]: https://stackoverflow.com/questions/3929774/how-much-overhead-is-there-when-creating-a-thread
 [3]: https://www.thegeekdiary.com/understanding-etc-security-limits-conf-file-to-set-ulimit/