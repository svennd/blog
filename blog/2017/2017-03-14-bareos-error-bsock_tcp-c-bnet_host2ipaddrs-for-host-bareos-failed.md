---
title: bareos error bsock_tcp.c bnet_host2ipaddrs() for host “bareos” failed
author: svennd

date: 2017-03-14T20:22:35+00:00
url: /bareos-error-bsock_tcp-c-bnet_host2ipaddrs-for-host-bareos-failed/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
After setting up my first job in bareos, I got this error :

<pre>sysadmin-fd JobId 4: Error: bsock_tcp.c:187 bnet_host2ipaddrs() for host "bareos" failed: ERR=Name or service not known
sysadmin-fd JobId 4: Fatal error: Failed to connect to Storage daemon: bareos:9103
bareos-dir JobId 4: Fatal error: Bad response to Storage command: wanted 2000 OK storage
, got 2902 Bad storage</pre>

_host2ipaddr_ should have given it away, but it still took some fiddling for me. I found out that bareos expects to have a DNS server set up properly to connect to clients and in this case to "bareos" (itself). If you are just testing, you can add the client/server's directly in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/hosts</code> or in Windows <code class="EnlighterJSRAW" data-enlighter-language="null">C:\Windows\System32\drivers\etc\hosts</code> but if you plan to go beyond the testing phase soon, it would be best to set up a FQDN that solves from both the clients and the server.

So quick and dirty :

<pre>/etc/hosts</pre>

add :

<pre>127.0.1.1 bareos</pre>

And the errors melted like snow under the sun 🙂