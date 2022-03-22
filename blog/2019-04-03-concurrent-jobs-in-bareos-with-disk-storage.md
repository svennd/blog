---
title: Concurrent Jobs in Bareos with disk storage
author: svennd

date: 2019-04-03T11:58:45+00:00
url: /concurrent-jobs-in-bareos-with-disk-storage/
thumbnail: /img/2020/05/krzysztof-kowalik-ciyfSzBmaKU-unsplash.jpg
categories:
  - SysAdmin
tags:
  - bareos

---
Surprisingly perhaps for newbies such as myself; Bareos will only start one backup job at once; meaning if you got 100 clients, there will be a serieus delay between the first and last job. So why is this happening ?

Bareos is a fork off Bacula and this was originally developed for the use of other types of media, such as tapes, DVD/CD and disks. In current age you might think only disks remain, but that's incorrect; While DVD/CD's are legacy media, tapes are still very much used. Here we use [LTO tapes][1] although not (yet?) in combination with Bareos. Tapes however are not random access and in allot situations only one tape can be loaded at once;

Hence only one backup could run at once, unless you had multiple devices. So disk storage users, have to trick Bareos to run multiple backups at once in order to work around the whole tape issue;

### More devices

Make more devices in the storage deamon; however be specific to only set 1 maximum concurrent jobs;

/etc/bareos/bareos-sd.d/device/FileStorage.conf

<pre>Device {
  Name = FileStorage
  Media Type = File
  #Archive Device = /var/lib/bareos/storage
  Archive Device = /storage/block1
  LabelMedia = yes;                   # lets Bareos label unlabeled media
  Random Access = yes;
  AutomaticMount = yes;               # when device opened, read it
  RemovableMedia = no;
  AlwaysOpen = no;
  Description = "File device. A connecting Director must have the same Name and MediaType."
 
}</pre>

Change this to :

<pre>Device {
  Name = FileStorage0
  Media Type = File
  #Archive Device = /var/lib/bareos/storage
  Archive Device = /storage/block1
  LabelMedia = yes;                   # lets Bareos label unlabeled media
  Random Access = yes;
  AutomaticMount = yes;               # when device opened, read it
  RemovableMedia = no;
  AlwaysOpen = no;
  Description = "File device. A connecting Director must have the same Name and MediaType."
  Maximum Concurrent Jobs = 1
}

Device {
  Name = FileStorage1
  Media Type = File
  #Archive Device = /var/lib/bareos/storage
  Archive Device = /storage/block1
  LabelMedia = yes;                   # lets Bareos label unlabeled media
  Random Access = yes;
  AutomaticMount = yes;               # when device opened, read it
  RemovableMedia = no;
  AlwaysOpen = no;
  Description = "File device. A connecting Director must have the same Name and MediaType."
  Maximum Concurrent Jobs = 1
}

Device {
  Name = FileStorage2
  Media Type = File
  #Archive Device = /var/lib/bareos/storage
  Archive Device = /storage/block1
  LabelMedia = yes;                   # lets Bareos label unlabeled media
  Random Access = yes;
  AutomaticMount = yes;               # when device opened, read it
  RemovableMedia = no;
  AlwaysOpen = no;
  Description = "File device. A connecting Director must have the same Name and MediaType."
  Maximum Concurrent Jobs = 1
}</pre>

Then let the director know :

<pre>/etc/bareos/bareos-dir.d/storage/File.conf</pre>

add the Devices :

<pre>Storage {
  Name = File
  Address = bareos                # N.B. Use a fully qualified name here (do not use "localhost" here).
  Password = "Re/vZBBjYg1Nm65DycOKtjiw0+WH7Byp3VTeifzhIlSl"
  Device = FileStorage3
  Device = FileStorage0
  Device = FileStorage1
  Device = FileStorage2
  Media Type = File
  Maximum Concurrent Jobs = 10
}</pre>

And finish up with restarting the service :

<pre>systemctl restart bareos-fd
systemctl restart bareos-sd
systemctl restart bareos-dir</pre>

Now concurrent jobs can run on these 4 devices 🙂

&nbsp;

 [1]: https://en.wikipedia.org/wiki/Linear_Tape-Open
