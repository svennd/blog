---
title: rsyslog separate file for logging
author: svennd

date: 2016-02-11T09:46:32+00:00
url: /rsyslog-separate-file-for-logging/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
Rsyslog is one of those tools that is on every modern Linux distro, but I never got to read the manual about it. [The website of rsyslog][1] is rather complex and not really attracting me to read it.  In essence rsyslog is just a logging mechanism. However it is so full featured, that configuration gets complex quick, a downside of powerful tools... I don't want to have a complex logging setup, most people are looking for simple "hacks". Like [centralizing r**syslog** output][2].

For example, I have an application I would like to have filtered out from_ /var/log/messages_, why ? Well _/var/log/messages_ is full of "useless verbose/debug output". In this example server (centos 7.1) I have messages from dhclient, chronyd, dbus, dbus-deamon, NetworkManager (euhk?), kernel messages from a IBM kernel module, ...  So its pretty messy, I could filter out all applications, but many of these are essential and work together, hence when a problem occurs its easy to have them in one place. My application however, is not really connected to the "state" of the machine, While I could just create my own logging mechanism, other developers/sysadmins will first look in /var/log/*, so why reinvent the wheel ? My target :

  ![in blue, services sending logging to /var/log/messages, in red my application that is sending to /var/log/tape, using rsyslog's filters.](/img//2015/07/25446912-1.png)
  

**Solution  
** My application calls logger (<code class="EnlighterJSRAW" data-enlighter-language="null">/usr/bin/logger</code>) with a  <code class="EnlighterJSRAW" data-enlighter-language="null">-t</code> from tag (I guess). By default they are logged in /var/log/messages. It is however possible to filter them out and put them in a separate file.

create _/etc/rsyslog.d/tape.conf_

<pre># separate tape logging in a separate file
if $programname == 'tape' then /var/log/tape.log
& stop
</pre>

The first line is going to look for "_-t tape_" and store them in /var/log/tape.log, the second line is telling _rsyslog_ that's pretty much it. Earlier versions of rsyslog also accepted <code class="EnlighterJSRAW" data-enlighter-language="null">&~</code> this is however deprecated, now the keyword stop seems to do it.

This would catch log messages such :

<pre>logger -t tape "my tapelog message"</pre>

Don't forget to reload rsyslog first. (restart would also work)

<pre>service rsyslog reload</pre>

pretty sure there are cleaner ways to do it, but this works. Hooray for small easy hacks!

 [1]: http://www.rsyslog.com/guides-for-rsyslog/
 [2]: https://www.svennd.be/setup-central-syslog-server-on-centos-7/
