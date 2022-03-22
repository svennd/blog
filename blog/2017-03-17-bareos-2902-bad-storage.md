---
title: 'bareos : 2902 Bad storage'
author: svennd

date: 2017-03-17T13:50:58+00:00
url: /bareos-2902-bad-storage/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin

---
While working with bareos backups, I got this error :

<pre>Fatal error: Bad response to Storage command: wanted 2000 OK storage , got 2902 Bad storage</pre>

<!--more-->

I thought it would be related to the underlying filesystem but a bit further in the error message log a hidden tip was to be found :

<pre>client-123 JobId 63: Error: bsock_tcp.c:187 bnet_host2ipaddrs() for host "bareoshead" failed: ERR=Name or service not known</pre>

However since this happened first, I would expect bareos to stop there. It doesn't. What's happening ? Well bareos could connect to the client it knows but the server "bareoshead" is not known to the client. That is why it fails in this particular case. After fixing <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/hosts</code> , I added this :

<pre>8.8.8.8 bareoshead</pre>

Next job ran successfully.