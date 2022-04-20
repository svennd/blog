---
title: reset supermicro’s admin IPMI from Linux console
author: svennd

date: 2017-02-24T10:07:41+00:00
url: /reset-supermicros-admin-ipmi-from-linux-console/
thumbnail: /img/2020/05/jonny-caspari_the_way.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - ipmi

---
Once _ipmitools_ are installed (if not see [this post][1]) its easy :

<pre>ipmitool -I open user set password 2 NEW_PASSWORD</pre>

(the capitals are the new password)

The 2 stands for second user, all the machines I have seen 1 is "guest" who has no access.

You can find a list of all users by issuing :

<pre>ipmitool user list 1</pre>

 [1]: https://www.svennd.be/install-supermicros-ipmi/