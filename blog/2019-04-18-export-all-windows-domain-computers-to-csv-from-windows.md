---
title: Export all windows domain computers to CSV from windows
author: svennd

date: 2019-04-18T11:39:39+00:00
url: /export-all-windows-domain-computers-to-csv-from-windows/
thumbnail: /img/2020/05/nightsky.jpg
categories:
  - SysAdmin
  - windows
tags:
  - windows

---
Doesn't happen often that I need Windows tools, but this worked nicely, so I though I share it with the future me that will have forgotten about this method;

On the domain server open a "Windows Powershell";

Import the module :

<pre>Import-Module ActiveDirectory</pre>

Then create the list of "enabled" users :

<pre>Get-ADComputer -Filter {enabled -eq $true} -properties *|select Name, OperatingSystem, LastLogonDate</pre>

This wil result in :

![](/img/2019/04/windows_domain.png) 

To export it in CSV :

<pre>Get-ADComputer -Filter {enabled -eq $true} -properties *|select Name, OperatingSystem, LastLogonDate | export-csv my_export.csv</pre>

Will create a file my_export.csv in csv format ! Enjoy.