---
title: Let’s Encrypt on …. any Linux distro!
author: svennd

date: 2016-02-13T19:29:45+00:00
url: /lets-encrypt-on-any-linux-distro/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
If you did not know yet, I'm a huge fan of [Let's Encrypt](https://letsencrypt.org), however, we need to give them some slack, they only publicly announced [1 year, 2 months, 26 days, 18 hours and 13 minutes ago](http://www.howlongagogo.com/date/2014/november/18).  In that time they created the entire backbone, code and structure to start giving out validated certificates, for <strong>free</strong>.  They currently have a beta sticker on still, and I do agree, there [github account](https://github.com/letsencrypt) is still very active with over 150 contributors pitching in.  The official client however has many dependencies, that many in fact that Centos 6 has only recently been supported. But what about Centos 5 ? or older Ubuntu LTS ? or any other Linux Distro ? What about when my apps are using a not supported python ?  Well that is where the Linux community shines most, someone thinks I can do this different and so [Lukas Schauer](https://github.com/lukas2511) ([personal](https://lukas.im) website) created a super tiny (821 lines) low-requirement shell script called _[dehydrated](https://github.com/lukas2511/letsencrypt.sh)_.  I still have little experience when it comes to certificates and https in general, but getting you're visitors a encrypted line to visit is now easier then ever !

#### Requirements
Most Linux distro's will have this already installed, so this should not make a problem. I'm using a Centos 32bit VPS for this example, but this should be doable for pretty much any Linux distro.
<pre>yum install nano wget curl sed grep mktemp git</pre>
That's it, for me only _nano_ and _wget _had to be installed on a Centos minimal! (note : [nano or ...](https://xkcd.com/378/))

#### Requirements
Its a bash script! Either download and chmod or download the entire package using git.
```
cd /opt
git clone https://github.com/lukas2511/dehydrated.git
cd dehydrated/
```

#### Requirements
You could run everything from console, but for automation it might be a good idea to configure it in a script itself. For that purpose it uses a file named _config.sh._ There is also a file _domains.txt_ for the domain names. I copied the example :
<pre>cp docs/examples/config config.sh
ln -s config.sh config</pre>
In config.sh <strong>remove the # (_hashtag)_ </strong>from all value's, the defaults should work well. The only exceptions are :
<pre>WELLKNOWN="/var/www/dehydrated"</pre>
and (change the e-mail address in to a <strong>working</strong> one!)
<pre>CONTACT_EMAIL="my.working@email.tld"</pre>
Now we need to create a file /opt/dehydrated/domains.txt there you need to add domain names you wanne get a certificate for. I tested it with one subdomain :
<pre>test.svennd.be</pre>
and that worked, however if you want multiple use :
<pre>svennd.be test.svennd.be alfa.svennd.be</pre>
Would validate svennd.be, test.svennd.be and alfa.svennd.be in one certificate, as alternatives. You can also read [up on the github](https://github.com/lukas2511/letsencrypt.sh#domainstxt) ,
That's it for configuration!
### Setting up the environment
For this server I am using Apache, you will have to find how to get this working with others webservers.
First make the directory (_WELLKNOWN_) we have used in the configuration :
<pre>mkdir /var/www/dehydrated/</pre>
_warning : using /home as WELLKNOWN might give problems on a default Apache installation. (you would need mod_userdir)_
On a clean install this was the config I used, most likely parts will already be there: (read comments)
<pre># allow multiple hosts on 1 IP
NameVirtualHost *:80

&lt;VirtualHost *:80&gt;
# documentroot
DocumentRoot /var/www/html

# servername
ServerName test.svennd.be
# vital :
Alias /.well-known/acme-challenge /var/www/dehydrated/

# this might not be needed
&lt;Directory "/var/www/dehydrated/"&gt;
   Header add Content-Type text/plain
&lt;/Directory&gt;

&lt;/VirtualHost&gt;</pre>
Restart apache with <code class="EnlighterJSRAW" data-enlighter-language="null">service httpd restart</code> , and then go ahead and create you're certificate!
#### Requirements
Now getting to the easy part, getting the certificate :
<pre>./dehydrated -c</pre>
This should result in something as :

<img aria-describedby="caption-attachment-920" loading="lazy" class="wp-image-920 size-full" src="/img//2015/07/25446736-1.jpg" width="339" height="81" srcset="/img/2015/07/25446736-1.jpg 339w, /img/2015/07/25446736-1-300x72.jpg 300w, /img/2015/07/25446736-1-4x1.jpg 4w" sizes="(max-width: 339px) 100vw, 339px" />

This will generate :
<pre>├── certs
│   └── domain.tld
│       ├── cert.csr
│       ├── cert.pem
│       ├── fullchain.pem
│       └── privkey.pem
├── config.sh
├── domains.txt
├── dehydrated
├── LICENSE
├── private_key.pem
└── README.md</pre>
From there you could just [use this tutorial ](https://www.svennd.be/lets-encrypt-with-centos-6/)for adding it to apache.
#### Requirements
During the testing I made some errors, you might encounter them as well.
#### Challenge invalid
<pre>ERROR: Challenge is invalid! (returned: invalid) (result: {"type":"http-01","status":"invalid","error":{"type":"urn:acme:error:unauthorized","detail":"Invalid response from http://domain.tld/.well-known/acme-challenge/[]: 500"},"uri":"https://acme-v01.api.letsencrypt.org/acme/challenge/","token":"","keyAuthorization":".-","validationRecord":[{"url":"http://domain.tld/.well-known/acme-challenge/","hostname":"domain.tld","port":"80","addressesResolved":[""],"addressUsed":""}]})</pre>
This happened for me when the directory was not accessible. Settings where wrong, be sure to test it with creating test files  (touch /my/.well-known/test.html) and then hitting them on the webserver.
#### Challenge invalid
<pre>./dehydrated --cron
# INFO: Using main config file /opt/dehydrated/config.sh
Processing domain.tld
 + Signing domains...
 + Creating new directory /opt/dehydrated/certs/domain.tld ...
 + Generating private key...
 + Generating signing request...
 + Requesting challenge for domain.tld...
  + ERROR: An error occurred while sending post-request to https://acme-v01.api.letsencrypt.org/acme/new-authz (Status 403)

Details:
{"type":"urn:acme:error:unauthorized","detail":"No registration exists matching provided key","status":403}</pre>
This weird one happened when 1) I did not enter a valid e-mail address and 2) an e-mail adres that was used by another domain.
#### Requirements
While the official method surely should be default, due to the way ACME is setup, Let's Encrypt allows for other clients, one of these is a very simple shell script, and that works in even the most variable environments.  A big thanks to [lukas2511](https://github.com/lukas2511) and [Let's Encrypt](https://letsencrypt.org) for making this possible! Happy Encrypting !
_Changes :_
<ul>
<li>Letsencrypt.sh has been renamed to <strong>dehydrated</strong> for legal reasons.</li>
<li>Slightly adapted the guide to reflect where config.sh comes from, also renamed some old _letsencrypt.sh_ references to dehydrated. (13/03/2017)</li>
</ul>
