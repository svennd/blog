---
title: 'Bareos Error: VSS API failure'
author: svennd

date: 2019-05-08T09:12:47+00:00
url: /bareos-error-vss-api-failure/
thumbnail: /img/2020/05/flood.jpg
categories:
  - SysAdmin
  - windows
tags:
  - bareos

---
When a Client job fails and reports :

<pre>Error: VSS API failure calling "BackupComplete". ERR=Object is not initialized; called during restore or not called in correct sequence.</pre>

Try to start the Service : VSS manually;

![](/img/2019/05/service_VSS.png) 

This solved the issue for a few Clients;