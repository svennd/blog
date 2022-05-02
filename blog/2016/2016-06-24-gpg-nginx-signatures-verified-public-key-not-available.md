---
title: 'GPG error nginx signatures couldn’t be verified because the public key is not available: NO_PUBKEY ABF5BD827BD9BF62'
author: svennd

date: 2016-06-24T10:21:51+00:00
url: /gpg-nginx-signatures-verified-public-key-not-available/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
A quickie before lunch :

<pre>GPG error: http://nginx.org lucid Release: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY ABF5BD827BD9BF62</pre>

Installing Nginx on my bash on windows 10 installation.  This is suprisingly very similar as on Ubuntu straight on bare metal, so the [fix is the same][1] :

In short :

<pre>wget http://nginx.org/packages/keys/nginx_signing.key
cat nginx_signing.key | sudo apt-key add - 
apt-get update
apt-get install nginx</pre>

live :

<pre class="EnlighterJSRAW" data-enlighter-language="shell">$ wget http://nginx.org/packages/keys/nginx_signing.key
--2016-06-24 11:36:25--  http://nginx.org/packages/keys/nginx_signing.key
Resolving nginx.org (nginx.org)... 2001:1af8:4060:a004:21::e3, 206.251.255.63, 95.211.80.227
Connecting to nginx.org (nginx.org)|2001:1af8:4060:a004:21::e3|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1561 (1.5K) [application/octet-stream]
Saving to: ‘nginx_signing.key’

100%[===========================================================================&gt;] 1,561       --.-K/s   in 0s

2016-06-24 11:36:25 (18.4 MB/s) - ‘nginx_signing.key’ saved [1561/1561]

$ cat nginx_signing.key | sudo apt-key add -
OK

$ apt-get install nginx
Reading package lists... Donebs
Building dependency tree
Reading state information... Done
The following extra packages will be installed:
 libssl0.9.8
The following NEW packages will be installed:
 libssl0.9.8 nginx
0 upgraded, 2 newly installed, 0 to remove and 0 not upgraded.
Need to get 1,194 kB of archives.
After this operation, 3,576 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu/ trusty-updates/universe libssl0.9.8 amd64 0.9.8o-7ubuntu3.2.14.04.1 [692 kB]
Get:2 http://nginx.org/packages/ubuntu/ lucid/nginx nginx amd64 1.8.0-1~lucid [502 kB]
Fetched 1,194 kB in 0s (2,337 kB/s)
Preconfiguring packages ...
Selecting previously unselected package libssl0.9.8:amd64.
(Reading database ... 26810 files and directories currently installed.)
Preparing to unpack .../libssl0.9.8_0.9.8o-7ubuntu3.2.14.04.1_amd64.deb ...
Unpacking libssl0.9.8:amd64 (0.9.8o-7ubuntu3.2.14.04.1) ...
Selecting previously unselected package nginx.
Preparing to unpack .../nginx_1.8.0-1~lucid_amd64.deb ...
----------------------------------------------------------------------

Thanks for using nginx!

Please find the official documentation for nginx here:
* http://nginx.org/en/docs/

Commercial subscriptions for nginx are available on:
* http://nginx.com/products/

----------------------------------------------------------------------
Unpacking nginx (1.8.0-1~lucid) ...
Processing triggers for ureadahead (0.100.0-16) ...
Setting up libssl0.9.8:amd64 (0.9.8o-7ubuntu3.2.14.04.1) ...
Setting up nginx (1.8.0-1~lucid) ...
runlevel:/var/run/utmp: No such file or directory
invoke-rc.d: policy-rc.d denied execution of start.
Processing triggers for libc-bin (2.19-0ubuntu6.9) ...
Processing triggers for ureadahead (0.100.0-16) ...</pre>

&nbsp;

 [1]: http://mailman.nginx.org/pipermail/nginx/2011-December/030918.html