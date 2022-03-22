---
title: Centos messages flooded with Create slice, Removed slice
author: svennd

date: 2020-04-21T07:38:00+00:00
url: /centos-messages-flooded-with-create-slice-removed-slice/
thumbnail: /img/2020/04/centos.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - rsyslog

---
In Centos 7/8 /var/log/messages is flooded with this :

<pre class="EnlighterJSRAW" data-enlighter-language="generic">Apr 21 08:40:01 tinky systemd: Created slice User Slice of root.
Apr 21 08:40:01 tinky systemd: Started Session 1709 of user root.
Apr 21 08:40:01 tinky systemd: Removed slice User Slice of root.
Apr 21 08:50:01 tinky systemd: Created slice User Slice of root.
Apr 21 08:50:01 tinky systemd: Started Session 1710 of user root.
Apr 21 08:50:01 tinky systemd: Removed slice User Slice of root.
Apr 21 09:00:01 tinky systemd: Created slice User Slice of root.
Apr 21 09:00:01 tinky systemd: Started Session 1711 of user root.</pre>

Highly annoying sometimes and unneeded writes IMHO; Luckely we can filter those out, or if you want you can [redirect them to a different file (see how)][1].

You can stop this with adding a rule to rsyslog ignoring all these, adding a rule like : <code class="EnlighterJSRAW" data-enlighter-language="generic">/etc/rsyslog.d/ignore-systemd-session-slice.conf</code>

<pre class="EnlighterJSRAW" data-enlighter-language="generic">if $programname == "systemd" and 
  (
  $msg contains "Starting Session" or 
  $msg contains "Started Session" or 
  $msg contains "Created slice" or
  $msg contains "Starting user-" or 
  $msg contains "Starting User Slice of" or 
  $msg contains "Removed session" or 
  $msg contains "Removed slice User Slice of" or 
  $msg contains "Stopping User Slice of"
  )
then stop</pre>

Provided by the [good folk of RedHat][2]. Don't forget to restart rsyslogd.

<pre class="EnlighterJSRAW" data-enlighter-language="generic">service rsyslogd restart
# or
systemctl restart rsyslog</pre>

happy logging (useful stuff)

 [1]: https://www.svennd.be/rsyslog-separate-file-for-logging/
 [2]: https://access.redhat.com/solutions/1564823