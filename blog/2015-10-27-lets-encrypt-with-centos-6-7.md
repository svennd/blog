---
title: Let’s Encrypt with Centos 6.7 !
author: svennd

date: 2015-10-27T15:16:56+00:00
url: /lets-encrypt-with-centos-6-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
> I updated this guide due to the public beta, as well as better support for python 2.6. [**See the new post**.][1]

I'm a huge fan of [Let's Encrypt][2], generally _https_ is safer then _http_ why ? Well when you send data (login data) over _http_ its child play to read out your password and login name. With _https_ a hacker would only see jumble. Until recently only a few free options where available to the webmaster/sysadmin/devops/... first was [self signed certificates][3], these give you a horrible warning that the website is insecure, ironically its much saver then _http_. The second option was to use free certificate providers, such as [startssl.com](https://startssl.com" target="_blank) sadly it takes allot of work and even if you are used to their workflow it takes some time to redo them every year. Let's Encrypt is to be the game changer in the field, they will deliver free certificates with only a few commands, on top of that they focus on automating the proces,they also deliver a certificate that is trusted by browsers!

So where is the catch ? Well there is not really one, expect they work with open-source and community driven development, which means, not everything is available when they are going to launch. Such as support for Centos 6.X (due to the python 2.7 requirement!)

It is however rather easy to install python 2.7 on Centos! Not even other repo's are required!

<pre>#https://wiki.centos.org/AdditionalResources/Repositories/SCL
yum install centos-release-SCL && yum update

# install python 2.7
yum install python27

# activate it
scl enable python27 bash

# install other python dependencies
yum install python27-python-devel python27-python-setuptools python27-python-tools python27-python-virtualenv

# these would be installed automaticly by the client but I prefer to do it myself
yum install augeas-libs dialog gcc libffi-devel openssl-devel python-devel</pre>

After python 2.7 is installed you are ready to follow up with let's encrypt default tool :

<pre>git clone https://github.com/letsencrypt/letsencrypt
cd letsencrypt
./letsencrypt-auto --verbose

# during beta
./letsencrypt-auto --verbose --agree-dev-preview --server https://acme-v01.api.letsencrypt.org/directory certonly</pre>

Let's encrypt the web!

update: Seems let's encrypt is [working on support](https://github.com/letsencrypt/letsencrypt/issues/1046" target="_blank) for python2.6 (and centos as a result)  
update 9 nov. :  
1) updated article based on experience during beta of lets encrypt.  
2) this method only works on 64bit machines, since SCL is only available for 64bit os  
3) public beta has been pushed back to 3 december. ([source][4])

 [1]: https://www.svennd.be/lets-encrypt-with-centos-6/
 [2]: https://letsencrypt.org
 [3]: https://en.wikipedia.org/wiki/Self-signed_certificate
 [4]: https://letsencrypt.org/2015/11/12/public-beta-timing.html