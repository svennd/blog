---
title: fail2ban add ip manually
author: svennd

date: 2015-11-09T13:00:01+00:00
url: /fail2ban-add-ip-manually/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
My love for fail2ban is slowly decreasing, I had a problem a while ago, while fixed, it was not easy to find. (see [fail2ban 0.9.9 with centos 6.7 not adding ips to firewall][1]) Today, again, I noticed that my <code class="EnlighterJSRAW" data-enlighter-language="null">iptables -L</code> was empty, while my <code class="EnlighterJSRAW" data-enlighter-language="null">/var/log/secure</code> was full (~70k lines, in one day) and fail2ban was doing absolutly nothing ... 🙁

I thought my older version of iptables (16.el6, 1.4.7) was the cause again, but it seems its not. Since I can use fail2ban to add ip's to iptables. I found the command, on the website by the original patch creator 🙂  Great tool if you wanne ban a range quickly!

<pre>fail2ban-client set ssh-iptables banip 123.456.789.001</pre>

_[source][2]_

 [1]: http://www.svennd.be/fail2ban-0-9-3-centos-6-7-iptables-no-longer-adding-ips/
 [2]: http://blogs.buanzo.com.ar/2009/04/fail2ban-patch-ban-ip-address-manually.html