---
title: Adding a Windows Client to bareos
author: svennd

date: 2017-03-21T14:06:01+00:00
url: /adding-a-windows-client-to-bareos/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin
  - windows
tags:
  - bareos

---
Since most of us work in a mixed environment (at best), we have to deal with all environments when it comes to backups. So here is how to install Bareos on Windows. There are two methods described in the manual. However I'm doing the GUI method. (It's Windows after all) You can download the [latest Windows Bareos binary package here][1].

<!--more-->

Installing on Windows is pretty straightforward :  (use defaults)

![Client software selection bareos](/img/2017/03/selection.png)

The **Client Name**, this can be a recognizable name and doesn't have to be network name.  
The **Director Name**, has to be the same name as the director name, for default installs this is bareos-dir.  
The **Password, **is generated in by default.  
The **Network Address**, should either be a solvable name or an IP address.  
The **Client Monitor Password**, can also be left as it was generated.

[![](/img/2017/03/setup.png)][2]

Next you get this screen, this can be copy pasted in :

<pre>/etc/bareos/bareos-dir.d/client/</pre>

[![](/img/2017/03/config_for_dir.png)][3]

&nbsp;

And that's it ! Upon next reboot the service runs and Bareos can backup from this client.

 [1]: http://download.bareos.org/bareos/release/latest/windows/
 [2]: /img/2017/03/setup.png
 [3]: /img/2017/03/config_for_dir.png
