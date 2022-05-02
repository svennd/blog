---
title: Running Nginx on Bash for Windows 10
author: svennd

date: 2016-06-24T14:05:34+00:00
url: /running-nginx-on-bash-for-windows-10/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - code
  - Linux
  - SysAdmin
  - webdev
  - windows

---
I normally go for Apache as my go-to webserver, lately however I got a liking to Nginx, for no real reasons except for the fact it has a nicer config layout. (a bad reason is a reason non the less) So when I installed Bash for Windows 10, I when and tried Nginx, sadly it didn't work, and still doesn't work nicely. I however got it running, this is how : First, [install Nginx][1].

Then I wanted to ignite Nginx, but that did not work, starting Nginx failed cause of port 80 was in use.

<pre>2016/06/24 11:37:22 [emerg] 2710#0: bind() to 0.0.0.0:80 failed (98: Address already in use)</pre>

Since I am running two systems (Linux and Windows 10) there are plenty of places to look. I started with the location I know best and was most unlikely the problem : Linux.  Using <code class="EnlighterJSRAW" data-enlighter-language="null">netstat -tulpn | grep 80</code> I verified nothing was running on this freshly installed UbuntuBash. So onto W10, I know Skype, Apache, ... uses port 80, but that did not run, so the hunt continued. I finally found -using <code class="EnlighterJSRAW" data-enlighter-language="null">resmon.exe</code>- under _Listener-port_  the problem, port 80 was being used by System (PID 4) ... not something you can easily kill ...

  ![PID 4 (Sytem) was listening on port 80. (no longer seen here)](/img//2015/07/25441312-1.png)

I dug a bit further and found this [useful post][2]. You have to stop some service, this is done using :

  * windows key + x
  * select console (admin)
  * run <code class="EnlighterJSRAW" data-enlighter-language="null">net stop http</code>

  ![net stop http in Windows 10 (in dutch)](/img//2015/07/25441536-1.png)


<pre>C:\WINDOWS\system32&gt;net stop http
De volgende services zijn afhankelijk van de HTTP Service-service.
Als u de HTTP Service-service stopt, worden deze services eveneens gestopt.

   World Wide Web Publishing-service
   SSDP Discovery
   Print Spooler
   HomeGroup Provider
   Function Discovery Resource Publication
   Function Discovery Provider Host

Wilt u doorgaan met deze bewerking? (J/N) [N]: j
De World Wide Web Publishing-service-service wordt gestopt.
De World Wide Web Publishing-service-service is gestopt.

De SSDP Discovery-service wordt gestopt.
De SSDP Discovery-service is gestopt.

De Print Spooler-service wordt gestopt.
De Print Spooler-service is gestopt.

De HomeGroup Provider-service wordt gestopt.
De HomeGroup Provider-service is gestopt.

De Function Discovery Resource Publication-service wordt gestopt.
De Function Discovery Resource Publication-service is gestopt.

De Function Discovery Provider Host-service wordt gestopt.
De Function Discovery Provider Host-service is gestopt.


De HTTP Service-service is gestopt.</pre>

After that I retried nginx to once again be shown an error :

<pre>2806#0: ioctl(FIOASYNC) failed while spawning "worker process" (22: Invalid argument)</pre>

That was solved using this -still open- [bugreport on github][3]. The essence is add the following to /etc/nginx/nginx.conf :

<pre>master_process off;
#daemon off;</pre>

Now to be honest I have got no idea, what any of those parameters do.  (note) I tried w/o the daemon parameter and it works as well, so I only use master_process and everything seems to "work". (**different from the solution on github**!)

After this, Nginx is running nicely on my BashOnWindows. Pretty nice work, Windows !

// update  28/06

On my second machine I also had to change <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/nginx/sites-available/default</code> the following line :

<pre>server {
        listen 80 default_server;
        listen [::]:80 default_server ipv6only=on;</pre>

to

<pre>server {
        listen 80 default_server;
        #listen [::]:80 default_server ipv6only=on;</pre>

This probably cause some ipv6 problem. (this is development only anyway!)

&nbsp;

 [1]: https://www.svennd.be/gpg-nginx-signatures-verified-public-key-not-available/
 [2]: http://stackoverflow.com/questions/788348/how-do-i-free-my-port-80-on-localhost-windows/16243333#16243333
 [3]: https://github.com/Microsoft/BashOnWindows/issues/68
