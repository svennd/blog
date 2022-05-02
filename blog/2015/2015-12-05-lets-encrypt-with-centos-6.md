---
title: lets encrypt, with Centos 6.
author: svennd

date: 2015-12-05T14:03:18+00:00
url: /lets-encrypt-with-centos-6/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
> **Note : **There are alternatives ways of getting lets encrypt to work in non-default environments, one is described in my new article : [Let's Encrypt on ... any Linux distro][1]

Let's encrypt the web, an easy, automated and free method to get https for your website. I already explained how you could install [letsencrypt on centos 6.7][2], but things on the interwebz go fast. So fast that in fact the tutorial is already deprecated. Since \*beta\* support has been added for Python 2.6, now Centos 6.X should work out of the box. Spoiler : it doesn't yet. (hence the beta label by letsencrypt) This guide should help to get _https_ in a not yet fully supported environments (such as Centos 6). As you might have noticed, also svennd.be is now running on https! (not cause its really necessary, but it is cool isn't it ? :P)

**My start point**

  * I have a Centos 6.X configured and yum-cron updated nightly
  * I have apache (http) running multiple domains and is a bit configured
  * I have 0 experience with SSL setup in http (Trust me, I have never done this before, successfully)

  <img aria-describedby="caption-attachment-920" loading="lazy" class="wp-image-920 size-full" src="/img//2015/07/25446736-1.jpg" width="339" height="81" srcset="/img/2015/07/25446736-1.jpg 339w, /img/2015/07/25446736-1-300x72.jpg 300w, /img/2015/07/25446736-1-4x1.jpg 4w" sizes="(max-width: 339px) 100vw, 339px" />
Let's Encrypt the web, this is where my https story began!


**Getting the certificate**

The first part is easy, the docs help out allot and since we all read them just after the terms and services. Right guys/gals ?

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># copy the software
cd /opt
git clone git clone https://github.com/letsencrypt/letsencrypt
cd letsencrypt</pre>

Now the next part would be to start the tool and it should help you there, the problem is, this requires to bind to port 80, which is obviously in use, by apache (httpd). So that won't work, also if you run this with Python 2.6 (Centos 6X) you will get a warning and it won't wanne do anything without you telling it to go in <code class="EnlighterJSRAW" data-enlighter-language="shell">--debug</code> mode.

There is however an alternative plugin included, which uses the webroot of the domain (in Apache words : _DocumentRoot_). Now _Let's Encrypt_ does not give out wildcard certificates, which means that you do not get *.svennd.be validated, instead you can get **www**.svennd.be, svennd.be, **alfa**.svennd.be, **beta**.svennd.be, ... just remember that you have to request those **at the same time** when you request the certificate, if you repeat the process, they won't work. Since we aim to automate,  I like to use as little as possible command line arguments, so I made a config file.

create <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/letsencrypt/cli.ini</code>

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># the default is 2048 (more is better)
rsa-key-size = 4096

# plugin
authenticator = webroot

# webroot
webroot-path = /var/www/svennd/

# domains
domains = svennd.be,www.svennd.be

# flags
# renew is good for automation
renew-by-default</pre>

Note : change the domain names to your domain name(s).

Now we can run the tool :

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">/opt/letsencrypt/letsencrypt-auto --config /etc/letsencrypt/cli.ini --debug certonly</pre>

Since I am on a not supported system I need the  <code class="EnlighterJSRAW" data-enlighter-language="shell">--debug</code> flag. If everything goes as planned you should be congratulated as followed :

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">Congratulations! Your certificate and chain have been saved at
  /etc/letsencrypt/live/www.svennd.be/fullchain.pem. Your cert will
  expire on 2016-03-04. To obtain a new version of the certificate in
  the future, simply run Let's Encrypt again.
- If like Let's Encrypt, please consider supporting our work by:
 Donating to ISRG / Let's Encrypt: https://letsencrypt.org/donate
 Donating to EFF: https://eff.org/donate-le</pre>

**Possible errors  
** Since It already took me some time to get here know that these errors are also rather common;

**error:connection**

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false" data-enlighter-language="shell">The following 'urn:acme:error:connection' errors were reported by
the server:</pre>

Which means it has no access to the server in general, best start point would be to check firewall or connection setting. The server should be publicly accessible during the webroot challenge.

**error:unauthorized**

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">FailedChallenges: Failed authorization procedure. cert.svennd.be (http-01): urn:acme:error:unauthorized :: The client lacks sufficient authorization :: Invalid response from http://cert.svennd.be/.well-known/acme-challenge/SOME_HASH [128.199.41.151]: 404
</pre>

I banged my head on this one, I received this error when I moved my website and configuration from http to https, this made the location unreachable. But it would be something you would also receive if your webroot is different from normal and you just copy-pasted the config. The webroot is the directory where users get their "index.php/html/asp/..." page from. For allot users thats somewhere here : /var/www/public\_html/my\_domain/ If you are not sure, its _DocumentRoot _in the configuration of Apache. Another way to know is to create a file "test.html" and go to your website : domain.ext/test.html a 404 means its not in the right directory. (you expect an empty white page)  Be sure that yourdomain.ext/.well-known/* is accessible ! T_hx to Luis for pointing this out._

**error:rateLimited**

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">Error: urn:acme:error:rateLimited :: There were too many requests of a given type :: Error creating new cert :: Too many certificates already issued for: svennd.be</pre>

This happens when you have played to much with them 😀 The solution is simple and hard, its called : wait it out. As long as the beta is in, they will rateLimit rather strongly, I believe not to many people will see this, after the initial beta period.

**error code 1 in cryptography**

<pre>Command "/root/.local/share/letsencrypt/bin/python2.7 -c "import setuptools, tokenize;__file__='/tmp/pip-build-cAuqmP/cryptography/setup.py';exec(compile(getattr(tokenize, 'open', open)(__file__).read().replace('\r\n', '\n'), __file__, 'exec'))" install --record /tmp/pip-rhCaoe-record/install-record.txt --single-version-externally-managed --compile --install-headers /root/.local/share/letsencrypt/include/site/python2.7/cryptography" failed with error code 1 in /tmp/pip-build-cAuqmP/cryptography</pre>

This happened due to limited resources during cryptokey generation. The solution was to create more free memory, although one should never go straight to production server without testing SSL first.

**Errno 22**

<pre>OSError: [Errno 22] Invalid argument: ‘/etc/letsencrypt/live/cert.pem’ letsencrypt</pre>

It only happened [during a server move, see the post][4].

**Activate the SSL in Apache**

Now I assume somehow you got to the point where you got congratulated and created the certificate. This would mean that you got four new files in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/letsencrypt/live/www.svennd.be/</code> , you would see cert.pem, chain.pem, fullchain.pem, privkey.pem.

I have Apache 2.2.15 (<code class="EnlighterJSRAW" data-enlighter-language="null">yum info httpd</code>) and by default it won't listen to port 443. So we need to add this :

In <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/httpd/conf/httpd.conf</code> find <code class="EnlighterJSRAW" data-enlighter-language="null">Listen 80</code> and add

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">Listen 443</pre>

After that you can adapt your virtualhost website configuration, I work with _VirtualHost *:80.  _My config looked like this :

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">&lt;VirtualHost *:80&gt;
        # server setup
        ServerName svennd.be
        ServerAlias www.svennd.be
        ServerAdmin webmaster@svennd.be
        DocumentRoot /var/www/svennd

        &lt;Directory "/var/www/svennd"&gt;
                AllowOverride All
                Order allow,deny
                Allow from all
        &lt;/Directory&gt;
&lt;/VirtualHost&gt;</pre>

I wanted to have both http and https running and after that is working (you want to check if everything works in https first)! Permanently redirect all traffic to https. To do that pretty much copy the virtualhost 80 to virtualhost 443. (full example, change to your domain!)

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">LoadModule ssl_module modules/mod_ssl.so

&lt;VirtualHost *:443&gt;
 # server setup
 ServerName svennd.be
 ServerAlias www.svennd.be
 ServerAdmin webmaster@svennd.be
 DocumentRoot /var/www/svennd

 # ssl setup
 SSLEngine ON
 SSLProtocol all -SSLv2 -SSLv3
 SSLHonorCipherOrder On
 SSLCipherSuite "EECDH+ECDSA+AESGCM EECDH+aRSA+AESGCM EECDH+ECDSA+SHA384 EECDH+ECDSA+SHA256 EECDH+aRSA+SHA384 EECDH+aRSA+SHA256 EECDH+aRSA+RC4 EECDH EDH+aRSA RC4 !aNULL !eNULL !LOW !3DES !MD5 !EXP !PSK !SRP !DSS !RC4"

 SSLCertificateFile /etc/letsencrypt/live/www.svennd.be/cert.pem
 SSLCertificateKeyFile /etc/letsencrypt/live/www.svennd.be/privkey.pem
 SSLCertificateChainFile /etc/letsencrypt/live/www.svennd.be/chain.pem

 &lt;Directory "/var/www/svennd"&gt;
 AllowOverride All
 Order allow,deny
 Allow from all
 &lt;/Directory&gt;
&lt;/VirtualHost&gt;

&lt;VirtualHost *:80&gt;
 # server setup
 ServerName svennd.be
 ServerAlias www.svennd.be
 DocumentRoot /var/www/svennd
 &lt;Directory "/var/www/svennd"&gt;
 AllowOverride All
 Order allow,deny
 Allow from all
 &lt;/Directory&gt;
&lt;/VirtualHost&gt;</pre>

I also added that httpd has to load the ssl module, on default installation however ssl module is not installed! Fix that with : <code class="EnlighterJSRAW" data-enlighter-language="null">yum install mod_ssl</code> . After that remove <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/httpd/conf.d/ssl.conf</code> or comment it.

Now you have to restart your httpd service, before doing so test if the config is right : <code class="EnlighterJSRAW" data-enlighter-language="shell">service httpd configtest</code>

You expect : Syntax OK. If that is the case restart your webserver :

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">service httpd restart</pre>

Now both http and https should be available; If its not, first check if your firewall allows connections on 443. For me it did not, I filter on INPUT rules, so I only had to add it there :

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># add it
iptables -I INPUT -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT

# save it
service iptables save</pre>

Then my WordPress took both https and http. Next part is probably only for WP owners, so you can skip that.

**Getting WordPress to play nice with Lets-encrypt ssl**

Adapting WordPress itself is rather easy, in wp-admin -> Settings -> General -> change WP address and site address to both https:// domain.ext. After that, I noticed most of my images where broken due to using http:// (note : you would get mixed error, I already adapted my .htaccess) You could change that using MySQL query ([source][5]):

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">UPDATE wp_posts
SET post_content = ( Replace (post_content, 'src="http://', 'src="//') )
WHERE  Instr(post_content, 'jpeg') &gt; 0
        OR Instr(post_content, 'jpg') &gt; 0
        OR Instr(post_content, 'gif') &gt; 0
        OR Instr(post_content, 'png') &gt; 0;</pre>

That's all !

**All http request redirected to https, except for _.well-known _for renewal**

I had to allow _.well-known_ to be served over http, otherwise we can't renew the certificate. This is my .htaccess (from WP),  [this is useful tool for testing .htaccess files][6].

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">RewriteEngine On

# HTTP REDIRECT
# its a http page request
RewriteCond %{HTTPS} off

# its not .well-known
RewriteCond %{REQUEST_URI} !\.well-known

# perm redirect to https version
RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI}  [R,L]

# WORDPRESS REDIRECT
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
&lt;/IfModule&gt;</pre>

note : Change R to R=301 once you have tested this configuration. (that is permanent)

Now add a cron, I added this <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/cron.weekly/certificate</code>

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">#!/bin/sh
/opt/letsencrypt/letsencrypt-auto --config /etc/letsencrypt/cli.ini --debug certonly

EXITVALUE=$?
if [ $EXITVALUE != 0 ]; then
 /usr/bin/logger -t letsencrypt "ALERT exited abnormally with [$EXITVALUE]"
fi
exit 0</pre>

This should update you're SSL certificate every week, this leaves enough time for you to see if something is not running as expected. If you should miss it don't worry, let's encrypt has you're email adres for just that case. You get a nice e-mail warning you :

<pre>Hello,

Your certificate (or certificates) for the names listed below will expire in 13 days (on 2016-02-03 12:18:00 +0000 UTC). Please make sure to renew your certificate before then, or visitors to your website will encounter errors.

example.ext

For any questions or support, please visit https://community.letsencrypt.org/. Unfortunately, we can't provide support by email.

Regards,
The Let's Encrypt Team</pre>

After you moved over, dump your url in [ssllabs][7] to see your SSL rating, some tweaks might be needed to get you to A+, but I believe its definitely worth it! ([svennd.be][8] is now A+)

Encrypted a tiny part of the web !

  <img aria-describedby="caption-attachment-962" loading="lazy" class="wp-image-962 size-full" src="/img//2015/07/25446728-1.png" width="81" height="36" srcset="/img/2015/07/25446728-1.png 81w, /img/2015/07/25446728-1-2x1.png 2w" sizes="(max-width: 81px) 100vw, 81px" />


  ![This seems like something to print out and put in a frame!](/img//2015/07/25449760-1.png)



 [1]: https://www.svennd.be/lets-encrypt-on-any-linux-distro/
 [2]: https://www.svennd.be/lets-encrypt-with-centos-6-7/
 [4]: https://www.svennd.be/oserror-errno-22-invalid-argument-etcletsencryptlivecert-pem-letsencrypt/
 [5]: https://css-tricks.com/moving-to-https-on-wordpress/
 [6]: http://htaccess.madewithlove.be
 [7]: https://www.ssllabs.com
 [8]: https://www.ssllabs.com/ssltest/analyze.html?d=svennd.be
