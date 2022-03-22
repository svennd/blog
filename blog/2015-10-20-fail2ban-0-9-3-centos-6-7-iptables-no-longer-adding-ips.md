---
title: 'fail2ban 0.9.3 + Centos 6.7 : iptables no longer adding ip’s'
author: svennd

date: 2015-10-20T12:26:52+00:00
url: /fail2ban-0-9-3-centos-6-7-iptables-no-longer-adding-ips/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
<p>On the public site, allot of errors where being logged in /var/log/messages, but no ip's showed up on</p>
<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">iptables -L</pre>
<p>meaning there where no active bans, however fail2ban reported (in /var/log/messages)</p>
<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">fail2ban.actions[]: NOTICE [ssh-iptables] 43.229.**.** already banned</pre>
<p>Meaning it found an attacker, that it knows about (in its database/logging mechanism) but still kept on hitting the server. After some digging I found this rather large and verbose error :</p>
<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">fail2ban.action[]: ERROR iptables -w -D INPUT -p tcp --dport ssh -j f2b-SSH#012iptables -w -F f2b-SSH#012iptables -w -X f2b-SSH -- stderr: "iptables v1.4.7: option `-w' requires an argument\nTry `iptables -h' or 'iptables --help' for more information.\niptables v1.4.7: option `-w' requires an argument\nTry `iptables -h' or 'iptables --help' for more information.\niptables v1.4.7: option `-w' requires an argument\nTry `iptables -h' or 'iptables --help' for more information.\n"</pre>
<p>After some debugging of the jail.local, I found nothing out of it, and googling fail2ban iptables did not bring much to the table, until at page two of google I found this [Russian ](http://www.linux.org.ru/forum/admin/12036639)support forum telling me that the -w option should be removed, while I believe it, my iptables (1.4.7, 1.el6) has -w option.</p>
<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">iptables v1.4.7: option `-w' requires an argument</pre>
<p>In the end, I found the original [enhancement request](https://github.com/fail2ban/fail2ban/issues/1122), and fixing it is easy :</p>
<p>find in /etc/fail2ban/action.d/iptables-common.conf</p>
<pre># Option:  lockingopt
# Notes.:  Option was introduced to iptables to prevent multiple instances from
#          running concurrently and causing irratic behavior.  -w was introduced
#          in iptables 1.4.20, so might be absent on older systems
#          See https://github.com/fail2ban/fail2ban/issues/1122
# Values:  STRING
lockingopt = -w
</pre>
<p>Replace it with</p>
<pre># Option:  lockingopt
# Notes.:  Option was introduced to iptables to prevent multiple instances from
#          running concurrently and causing irratic behavior.  -w was introduced
#          in iptables 1.4.20, so might be absent on older systems
#          See https://github.com/fail2ban/fail2ban/issues/1122
# Values:  STRING
lockingopt =</pre>
<p>Reloading fail2ban (<code class="EnlighterJSRAW" data-enlighter-language="null">service fail2ban restart</code>) will get the bad boys banned in no time again !</p>
