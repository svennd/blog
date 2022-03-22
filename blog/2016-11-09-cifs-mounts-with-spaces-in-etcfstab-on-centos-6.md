---
title: cifs mounts with spaces in /etc/fstab on Centos 6
author: svennd

date: 2016-11-09T13:38:52+00:00
url: /cifs-mounts-with-spaces-in-etcfstab-on-centos-6/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Quick hack, when adding cifs to /etc/fstab you can't use quotes to bridge a space in Linux; this will fail :

<pre>"//SERVER/NAME WITH SPACE/" /local_share/  cifs  username=user,password=paswd,iocharset=utf8,sec=ntlm  0  0</pre>

that tricks works in shares from cmd :

<pre>mount -t cifs -o user=user,pass=pasw "//SERVER/SHARE WITH SPACE/" /local/share</pre>

so for /etc/fstab, you have to use **\040** which unsurprisingly maps to [space in octal][1]. Well another thing learned! Here is what should work in /etc/fstab

<pre>//SERVER/SHARE\040WITH\040SPACES/ /local/share/  cifs  username=user,password=paswd,iocharset=utf8,sec=ntlm  0  0</pre>

Happy mounting Windows shares 🙂

&nbsp;

 [1]: https://www.ascii-code.com/
