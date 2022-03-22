---
title: 'Server fails to boot with lvm error : “Invalid argument for –available: ay Error during parsing of command line.”'
author: svennd

date: 2015-11-28T11:11:12+00:00
url: /server-fails-to-boot-with-lvm-error-invalid-argument-for-available-ay-error-during-parsing-of-command-line/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
One of our servers had problems with nfs service (network file system), considering it was a storage server mainly used for nfs, that's a problem. We where monitoring it before and after and strangely -for a Linux achine- nothing really popped up. Last week the same server had a out of memory state that pretty much locked up the kernel and as such the server. A reboot "fixed" it, but I assumed a new kernel had been pushed and was now being a pain in the ass. Like any production machine it could not be rebooted during the day until, -after some discussion- we noticed that the<img loading="lazy" class="alignnone wp-image-948" src="/img//2015/07/29733608-1.jpg" width="300" height="288" srcset="/img/2015/07/29733608-1.jpg 788w, /img/2015/07/29733608-1-300x288.jpg 300w, /img/2015/07/29733608-1-768x737.jpg 768w, /img/2015/07/29733608-1-1x1.jpg 1w" sizes="(max-width: 300px) 100vw, 300px" /> jobs running for a few weeks where in fact not doing anything. I checked the updates and to my surprise this machine had not been updated for a long while, by default I install yum-cron (unattended-upgrades for debian), on any system I install, but this one was before my time. There where around 1gb of updates ready, and that's allot that can go wrong. Considering the OOM we decided quickly that installing the latest kernel would be the best thing to do, as clearly this OOM was fixed in the latest version. Since allot some tools depend on that kernel, we decided to take all the updates. During the updates however the machine locked up, active ssh connections got unresponsive, the monitor on the machine was not getting any signal and the worst : the USB keyboard & mouse did not get any power.

Clearly the machine was not functioning and we had to reset the machine. We where welcomed on the reboot with this error :

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">Server fails to boot with lvm error : "Invalid argument for --available: ay Error during parsing of command line."</pre>

That exact error gives back one result on google, a [redhat article][1] (I wrongly assumed was behind a paywall, in fact its just behind a login/registration wall) In fact there is not really an article behind. The only thing they give is the "Environment" it can occur in :

  * initscripts versions greater or equal to 9.03.32-1 and below 9.03.38-1
  * lvm versions below lvm2-2.02.97-2.el6

Normally yum will protect you from these kind of problems, so by breaking the transaction on yum (server reset) I assume on of those two got on the bad version and gave me the trouble. I used a rescue USB stick and checked the ext4 (root and home) for errors, both where ok. Then I rebooted the machine since installing a new version of lvm, lvm-libs and initscripts looked very difficult from inside a debian distro. The root partition however remained read-only. There is this tiny hack to fix that :

<pre>mount -o remount,rw /lvm_root /</pre>

Which will remount the root partion in read/write mode. Most people would now be able to finish yum with :

<pre>yum-complete-transaction</pre>

To bad for me that failed.  The advice given then is to check for dupes <code class="EnlighterJSRAW" data-enlighter-language="null">package-cleanup --dupes</code>, (since that was one of the problems) However checking for dupes and cleaning them, can -in my case- be **dangerous:** it wanted to remove _glibc_ and 2.1 Gb of system application, that really bad news, so don't let it do that!

The thing that got me on track was : <code class="EnlighterJSRAW" data-enlighter-language="null">yum-complete-transaction --cleanup-only</code> this removed the constant error telling me to finish the transaction. After that I wanted to remove the duplicates, you cannot use yum, cause yum will remove dependencies :

<pre>rpm --nodeps --noscript -e package</pre>

If you like me had around 1gb of updates, this is a easy hack :

<pre># Store duplicate-list in a file.
package-cleanup --dupes &gt; duplicates.txt

# Remove odd lines.
awk 'NR%2==0' duplicates.txt &gt; single-duplicates.txt

# Replace new lines with spaces.
awk '{ printf "%s ", $0 }' single-duplicates.txt &gt; duplicates.txt

# Copy-paste content of duplicates after command.
rpm --nodeps --noscripts -e [paste here]
</pre>

[source][2], also works for Centos 6X. After that I reinstalled allot of the packages. I tried to recall the problematic (initscripts, lvm, glibc, kernel, kernel-headers,...) and made sure those where done.

Later I also found <code class="EnlighterJSRAW" data-enlighter-language="null">yum check all</code> that did not help too much as most was already fixed, but still worth the look!

After yum and rpm where both happy (updated) I rebooted the machine and until now its holding up like a champ. Also _to hell _ with the last sysadmin that got me in this mess! Install _yum-cron_ if you are to lazy to update regularly!

Also usefull :

  * [yum update fail][3]

 [1]: https://access.redhat.com/solutions/381313
 [2]: http://forums.fedoraforum.org/showthread.php?p=1686441#post1686441
 [3]: https://destefano.wordpress.com/2013/08/13/yum-update-fail/