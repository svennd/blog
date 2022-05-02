---
title: 'Centos 8 : install LSI MegaRAID SAS 2008'
author: svennd

date: 2021-04-08T13:02:32+00:00
url: /centos-8-install-lsi-megaraid-sas-2008/
thumbnail: /img/2020/05/aleks-marinkovic-computer-ducks.jpg
categories:
  - Linux
  - SysAdmin

---
Okay so while playing with older hardware I faced an issue on Centos 8 where my RAID controller was not being recognized;

I found this method to be working;

from [ElRepo][1]

```yum install https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm```

then install

```yum install kmod-megaraid_sas```

I got this "warning" during installation :  
```Failed to add dependency on unit, unit systemd-ask-password-plymouth.service does not exist.```

But after a reboot the raid controller just worked as if nothing had ever happened 🙂

 [1]: http://elrepo.org/tiki/HomePage
