---
title: 'systemd: Configuration file … is marked executable. Please remove executable permission bits. Proceeding anyway.'
author: svennd

date: 2016-02-22T11:20:29+00:00
url: /systemd-configuration-file-is-marked-executable-please-remove-executable-permission-bits-proceeding-anyway/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Sometimes you just need /var/log/messages only showing relevant errors, I already found out how to [separate the logging][1] in files; but this one you can't filter out; Its a permission "bug" in the distro (Centos 7.2). I got it for these two, but there are more on the open.

<pre>systemd: Configuration file /usr/lib/systemd/system/ebtables.service is marked executable. Please remove executable permission bits. Proceeding anyway.
systemd: Configuration file /usr/lib/systemd/system/wpa_supplicant.service is marked executable. Please remove executable permission bits. Proceeding anyway.
</pre>

Just change the permission to 0644 from the original 0755 :

<pre>chmod 0644 /usr/lib/systemd/system/wpa_supplicant.service
chmod 0644 /usr/lib/systemd/system/ebtables.service</pre>

Another line squished !

 [1]: https://www.svennd.be/rsyslog-separate-file-for-logging/