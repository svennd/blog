---
title: dehydrated using cloudflare hook
author: svennd

date: 2017-02-01T07:22:28+00:00
url: /dehydrated-using-cloudflare-hook/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS

---
After the fairy dust has settled on Let's Encrypt, allot of good solutions are out there to get a valid certificate using the ACME protocol ([see client list][1]). Most of the technique's use webroot as verification technique, while this is fine for most websites, it has some downsides. For one, you need to adapt (let it be adapted) the webserver configuration, to allow connection to the webroot, for allot of webservers this is difficult or annoying. The alternative solution is to add a TXT record in the DNS manager and verify the domain using that method. This however, is more difficult to automate, but if you use _cloudflare_ like I do (at least for DNS) you can automate it pretty easely, using [dehydrated][2] (previously known as _letsencrypt.sh_) and the cloudflare hook (by [kappataumu][3]). This is how I generally install it on non-public servers that require https.

### Requirements

Some of these might already be installed but for a clean server they aren't.  You do need _epel-release_, so after that install, we need to update _yum_ so that the packages and dependency's can be found.

<pre>yum install epel-release
yum update
yum install git python-pip gcc python-devel libffi-devel openssl openssl-devel
</pre>

### Setup

Now its time to get the software that runs this setup : I for one do this in /opt but its totally up to you.

<pre>cd /opt
git clone https://github.com/lukas2511/dehydrated</pre>

Next we are going to download the hook for cloudflare DNS :

<pre>cd dehydrated
mkdir hooks
git clone https://github.com/kappataumu/letsencrypt-cloudflare-hook hooks/cloudflare</pre>

Now we need to run the requirements of python, on Centos 6/7, the default python version is 2.X So run :

<pre>pip install -r hooks/cloudflare/requirements-python-2.txt</pre>

For linux distro's where Python 3.x is default use :

<pre>pip install -r hooks/cloudflare/requirements.txt</pre>

If that finishes ok, its time to make a config file for dehydrated. (not strictly necessary but for a cron & automating allot easier!)

### Config

Dehydrated uses a file "config", I however dislike this and renamed it config.sh (as its shell code) and linked a config file. (you could even put it in /etc where this would fit better) We also need a file _domains.txt _this is a space separated list of the domain + sub-domains, and if you have multiple domains, split them by a line break.

<pre>cd /opt/dehydrated
touch config.sh
ln -s config.sh config
touch domains.txt</pre>

You can find an example config + domains.txt in _docs/examples/._

This is a valid domains.txt :

<pre>svennd.be www.svennd.be
divebug.com www.divebug.com demo.divebug.com
</pre>

This will request two certificates, one for svennd.be and one for divebug.com the www is a sub-domein and also demo is a sub-domain.

My config : (stripped of sensitive information)

<pre># cloudflare settings
export CF_EMAIL=""
export CF_KEY=""

# letsencrypt.sh
export CHALLENGETYPE="dns-01"
export CONTACT_EMAIL="valid@mail.ext"
export HOOK="hooks/cloudflare/hook.py"
export RENEW_DAYS="60"</pre>

This will check if the current certificates are valid for at least 60 days (RENEW\_DAYS) if not it will get a new certificate. note that you need to change CONTACT\_EMAIL, as this will inform you if the renew hasn't happened and you did not notice.

CF\_EMAIL is the login email for Cloudflare. The CF\_KEY is an API key used to access Cloudflare API's you can find it under [my account][4]

<img aria-describedby="caption-attachment-1407" loading="lazy" class="wp-image-1407 size-full" src="/img/2017/01/api_key_cloudflare.png" width="965" height="504" srcset="/img/2017/01/api_key_cloudflare.png 965w, /img/2017/01/api_key_cloudflare-300x157.png 300w, /img/2017/01/api_key_cloudflare-768x401.png 768w" sizes="(max-width: 965px) 100vw, 965px" />

### Getting certificate

First time you need to register & accept the agreement of Let's Encrypt :

<pre>./dehydrated --register --accept-terms</pre>

If setup properly, you should now be able to run _./dehydrated -c _this would generate valid certificates : (something similar like 🙂

<pre># ./dehydrated -c
# INFO: Using main config file /opt/dehydrated/config
Processing svennd.be with alternative names: www.svennd.be
 + Checking domain name(s) of existing cert... unchanged.
 + Checking expire date of existing cert...
 + Valid till Apr  2 09:21:00 2017 GMT (Less than 91 days). Renewing!
 + Signing domains...
 + Generating private key...
 + Generating signing request...
 + Requesting challenge for svennd.be...
 + Requesting challenge for www.svennd.be...
 + CloudFlare hook executing: deploy_challenge
 + Settling down for 10s...
 + Responding to challenge for svennd.be...
 + CloudFlare hook executing: clean_challenge
 + Challenge is valid!
 + CloudFlare hook executing: deploy_challenge
 + Settling down for 10s...
 + Responding to challenge for www.svennd.be...
 + CloudFlare hook executing: clean_challenge
 + Challenge is valid!
 + Requesting certificate...
 + Checking certificate...
 + Done!
 + Creating fullchain.pem...
 + CloudFlare hook executing: deploy_cert
 + ssl_certificate: /opt/letsencrypt/certs/svennd.be/fullchain.pem
 + ssl_certificate_key: /opt/letsencrypt/certs/svennd.be/privkey.pem
 + Done!
Processing divebug.com with alternative names: www.divebug.com
 + Checking domain name(s) of existing cert... unchanged.
 + Checking expire date of existing cert...
 + Valid till Apr  2 09:22:00 2017 GMT (Less than 91 days). Renewing!
 + Signing domains...
 + Generating private key...
 + Generating signing request...
 + Requesting challenge for divebug.com...
 + Requesting challenge for www.divebug.com...
 + CloudFlare hook executing: deploy_challenge
 + Settling down for 10s...
 + Responding to challenge for divebug.com...
 + CloudFlare hook executing: clean_challenge
 + Challenge is valid!
 + CloudFlare hook executing: deploy_challenge
 + Settling down for 10s...
 + Responding to challenge for www.divebug.com...
 + CloudFlare hook executing: clean_challenge
 + Challenge is valid!
 + Creating fullchain.pem...
 + CloudFlare hook executing: deploy_cert
 + ssl_certificate: /opt/letsencrypt/certs/divebug.com/fullchain.pem
 + ssl_certificate_key: /opt/letsencrypt/certs/divebug.com/privkey.pem
 + Done!</pre>

_note : I cheated and put the renew_days from 60 to 91, since let's encrypt certificates are valid for 90 days, this script would always request new certificates which is not needed._

I run this script in /etc/cron.weekly/cert.cron :

<pre>/opt/dehydrated/dehydrated -c
</pre>

For debugging purpose it is sometimes useful to run it without the need for a fully working config, in that case you can use :

<pre>./dehydrated -c -d svennd.be -t dns-01 -k 'hooks/cloudflare/hook.py'</pre>

That's it ! Fully configured certificate renewal process 🙂

 [1]: https://letsencrypt.org/docs/client-options/
 [2]: https://github.com/lukas2511/dehydrated
 [3]: http://kappataumu.com
 [4]: https://dash.cloudflare.com/login
