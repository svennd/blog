---
title: Adding a Linux client to bareos
author: svennd

date: 2017-03-10T15:15:35+00:00
url: /adding-a-linux-client-to-bareos/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Bareos is really powerful, but also pretty complex. If we take it step by step, we will get stuff up and running soon enough. Let's start simple by adding a client and setting up a backup job for the configuration of Linux servers (next post). Since I like to run <code class="EnlighterJSRAW" data-enlighter-language="null">yum-cron</code> its quit common for configuration files to get added or changed. It's also common for configuration to be made and forgotten about, but during a bare metal recovery of that server, its a great resource to have config files ! So let's go ahead and back that up.

<!--more-->

I was a bit disappointed that configuration can't be done from the bareos webui, from what I see its more like a status page then a real "UI". So the only option to configure is through the _bconsole_ and configuration files, and as a "Linux sysadmin" that is even better 🙂

#### Setting up the bareos client

Let's start at the client; We need to add the repository to the system. (This is for Centos 7 client)

<pre>wget -O /etc/yum.repos.d/bareos.repo http://download.bareos.org/bareos/release/latest/CentOS_7/bareos.repo</pre>

After this, you should be able to install the _bareos-filedaemon_ :

<pre>yum install bareos-fd</pre>

This is the daemon responsible for communicating with the _bareos-director_ on the bareos (backup) server. We need to enable the service to start at boot :

<pre>systemctl enable bareos-fd</pre>

We also need to start the bareos-fd, but first we need to generate the configuration file for the bareos-fd on the bareos-server.

#### Adding a bareos client to the server

Since the web interface can't be used (yet?) lets fire up <code class="EnlighterJSRAW" data-enlighter-language="null">bconsole</code>. Adding the client is done like this :

<pre>configure add client name=svennd address=192.168.0.2 password=SOME_PASSWORD</pre>

This should result in something like :

<pre>*configure add client name=svennd address=8.8.8.8 password=alfa123
Exported resource file "/etc/bareos/bareos-dir-export/client/svennd/bareos-fd.d/director/bareos-dir.conf":
Director {
  Name = bareos-dir
  Password = "[md5]f7180d44d3cad452f611e2dfdfd3b007"
}
Created resource config file "/etc/bareos/bareos-dir.d/client/svennd.conf":
Client {
  Name = svennd
  Address = 8.8.8.8
  Password = alfa123
}</pre>

After that the configuration for the bareos director has been made, the only configuration that still is missing is on the client, this has been made by the bconsole, and can be found in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/bareos/bareos-dir-export/client/svennd/bareos-fd.d/director/bareos-dir.conf</code> we need to copy that to <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/bareos/bareos-fd.d/director/</code> on the client machine.

This can be copied using scp to the client.

<pre>scp /etc/bareos/bareos-dir-export/client/svennd/bareos-fd.d/director/bareos-dir.conf root@8.8.8.8:/etc/bareos/bareos-fd.d/director/</pre>

Then on the client you can start the filedaemon :

<pre>systemctl start bareos-fd</pre>

And that's it, now we are ready to start [creating a backup job.][1]

 [1]: https://www.svennd.be/create-a-backup-job-on-bareos/