---
title: Installing passbolt on Centos 7
author: svennd

date: 2017-02-14T14:54:30+00:00
url: /installing-passbolt-on-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
_note : the creators of Passbolt have now created a guide for [installing on Centos 7][1] specifically._

I did not -physically- make it to [Fosdem 2017][2].  Luckily the guys from _Fosdem_ offered a live-stream for most part of the event, this gave the advantage for digital lurkers, like me, to jump from one talk to the next mid presentation, without being rude to presenter. While randomly wandering between two presentations, I stumbled upon [this _passbolt_ presentation][3], I had read of _passbolt_ before, but I was put off by the fact that it required a browser plugin.

In case you don't know _passbolt,_ it's a password managers aimed at managing passwords for teams. But don't take my word for it, go over to the _[passbolt ][4]_website and find out.

![passbolt install on centos](/img/2017/02/logo@2x-300x58.png)


After seeing the presentation, I now better understand how _passbolt_ works and why the browser plugin is needed. This alone however, did not convince me, but the fact that we can self-host the software and it is open source, does make me a fan. Even tho it is still in alpha, I tried it out. I'm not gone lie, after some real struggle, I got it to work. But since it is still alpha software this was to be expected. Here you can find the fool-proof (not!) guide to setup your own _passbolt_ instance on Centos. I used Centos 7 but it should work very similar for any RHEL distro, and if you are stuck it might even help with debian and friends distro's (Ubuntu, Mint,...).

note : The stack that I chose might not be the best/only option. (Nginx, MariaDB, PHP 7, Let's Encrypt)

### Setup Nginx

Perhaps you already have a working Nginx setup; in that case you can skip ahead.

First I install the Nginx repository, create <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/yum.repos.d/nginx.repo</code> With content :

<pre>[nginx]
name=nginx repo
# mainline : newest
baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/
# stable : older, no new features
#baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1</pre>

_Note : for other distro's you need to adapt this, for Centos 6 just replace 7 with 6, for more info [see this document.][5]_

Now we need to update yum & install it.

<pre>yum update
yum install nginx</pre>

Then configure your certificate using Let's Encrypt, I used my own guide with [_dehydrated_ and _Cloudflare_][6], in case you don't use C_loudflare_, you could use [this guide][7] or one of the other [installation methods][8] I have written in the past. Whatever method you chose, be sure to end up with a valid _Nginx_ configuration that has https on and uses this by default. ([demo config][9])

### Setup MariaDB

If I am correct currently only MariaDB/MySQL is supported for _passbolt_. The setup is rather straightforward and if you already have MariaDB running, you just need to create a database.

I took the vendor provided version, as that one will have the best support long term, and from what I see no cutting-edge features are required from the database:

<pre>yum install mariadb-server</pre>

Since I use Centos, I have to start the service manually, there is no need to go optimize to early so just use the default MariaDB setup.

<pre>service mariadb start</pre>

Make sure to run the install script, this cleans up the default MariaDB/MySQL settings a bit :

<pre>mysql_secure_installation</pre>

Then login to the database and create a database you want to use for _passbolt. _I took "passbolt" as name. (crazy I know)

<pre>mysql -u root -p</pre>

Create the database and exit nicely. (optional :  you could create a separate user here)

<pre>create database passbolt;
exit;</pre>

### Install PHP & gnupg

Since I'm writing this guide for _Centos_, the _PHP_ version in repository is still somewhere in PHP 5.3.* range. While security fixes are being backported and it is still "rather" safe, I decided not to go with this option. Instead I use PHP 7 which is faster and lighter on the hardware. I wouldn't be surprised if in the near future support for PHP 5.x is dropped from _passbolt_.

I like _webtatic´s_ repository for newer _PHP _versions, so I always take that repo, there are other repository's out there that might be better, so this is just a option so feel free to use alternatives.

Add _webtatic_ for Centos 7 using :

<pre>rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm</pre>

After running a <code class="EnlighterJSRAW" data-enlighter-language="null">yum clean all && yum update -y</code> we can run the install : (PHP 7.1 is currently the highest version for _webtatic's _repository)

<pre>yum install php70w-fpm php70w-opcache php70w-gd php70w-mysql php70w-mcrypt php70w-cli php70w-pear php70w-devel</pre>

You require these packages for :

  * php70w-fpm is the package _Nginx_ will rely on for understanding .php code
  * php70w-opcache is the package that gets the bytecode from .php files into the memory for faster access.
  * php70w-gd is required by _passbolt_, this includes the GD library.
  * php70w-mysql is required to connect to the MySQL/MariaDB database
  * php70w-mcrypt, is used for the mcrypt functions
  * php70w-cli is the PHP cli tool, its used for the installation off passbolt
  * php70w-pear is needed to install _pecl_, which is needed to install gnupg
  * php70w-devel is needed when we compile gnupg for _PHP_

Once we have these packages installed and setup (test them with Nginx!) its time to install gnupng, this can be done using the php70w-pear package that provided a binary _pecl. _I believe this is close relative to _pip_ in Python. Before I could install gnupng I needed _gpgme-devel_, since we are going to compile we also need gcc, which most will have installed already.

<pre>yum install gpgme-devel gcc</pre>

Once we have that try and run :

<pre>pecl install gnupg</pre>

This prints out the usual compile checks and text blobs, if successful you should see something like :

<pre>Build process completed successfully
Installing '/usr/lib64/php/modules/gnupg.so'
install ok: channel://pecl.php.net/gnupg-1.4.0
configuration option "php_ini" is not set to php.ini location
You should add "extension=gnupg.so" to php.ini</pre>

The guidelines are correct for Apache on Ubuntu, but for Centos the PHP modules should be loaded differently;  Under <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/php.d/</code> create a file <code class="EnlighterJSRAW" data-enlighter-language="null">gnupg.ini</code> with content :

<pre># load the gnupg module for passbolt
extension=gnupg.so</pre>

After that restart _php-fpm_ : (for the installation, which uses _php-cli, _a restart is not required)

<pre>service php-fpm restart</pre>

Now you can check if the module is loaded, if nothing is returned, the module is not installed / loaded.

<pre>php --info | grep 'gnupg support'</pre>

### Generate key

To generate the keys, I ran in some problems, I ran this test setup inside a container and perhaps the issue's are related to that.

Generating the key can be done using : <code class="EnlighterJSRAW" data-enlighter-language="null">gpg --gen-key</code> while this might work for you, for me it did not, it kept hanging once it was generating. Also the output was completely _fubar_. The reason _gpg_ got stuck, was that the machine had low entropy. Perhaps this is an issue using a container ? The console actually asks you to move around with the mouse, but on a console that is kinda hard.

You can check if entropy is low using <code class="EnlighterJSRAW" data-enlighter-language="null">cat /proc/sys/kernel/random/entropy_avail</code> this should return at least a triple digit number if not generating a key might take a long time or fail altogether. My value was jumping between 5-11, way to low. There are some tricks around, like running a find / in another console or dd some random stuff, but what worked for me was using a _Random Number Generator_ package. Install is easy :

<pre>yum install rng-tools</pre>

Then bind (?) it to /dev/urandom :

<pre>rngd -r /dev/urandom</pre>

A few seconds later, the value of _entropy_avail_ shot high enough to generate a key. Note that you cannot enter a password for the key, this is a restriction of _gnupg_. It will ask you for a password just hit enter, it will then tell you that a zero-length password is not a good idea, accept it anyway.

<pre>gpg --gen-key</pre>

<pre>gpg (GnuPG) 2.0.22; Copyright (C) 2013 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048)
Requested keysize is 2048 bits
Please specify how long the key should be valid.
         0 = key does not expire
      &lt;n&gt;  = key expires in n days
      &lt;n&gt;w = key expires in n weeks
      &lt;n&gt;m = key expires in n months
      &lt;n&gt;y = key expires in n years
Key is valid for? (0)
Key does not expire at all
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: svennd
Email address: user@mail.ext
Comment:
You selected this USER-ID:
    "svennd &lt;user@mail.ext&gt;"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
You need a Passphrase to protect your secret key.

You don't want a passphrase - this is probably a *bad* idea!
I will do it anyway.  You can change your passphrase at any time,
using this program with the option "--edit-key".

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: /root/.gnupg/trustdb.gpg: trustdb created
gpg: key A01B063E marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
pub   2048R/A03E 2017-02-14
      Key fingerprint = CE
uid                  svennd &lt;user@mail.ext&gt;
sub   2048R/CE 2017-02-14</pre>

&nbsp;

Note that you need the fingerprint of the generated key later. (the fingerprint is shown in the output)

Next we need to export the public and private key, in a format we can use. I like to put these keys in /opt but that might not be the best location. (might not be the worst either)

<pre>mkdir -p /opt/passbolt/
gpg --armor --export-secret-keys user@domain.ext &gt; /opt/passbolt/serverkey.private.asc
gpg --armor --export user@domain.ext &gt; /opt/passbolt/serverkey.asc</pre>

### Install _Passbolt_

Installing _passbolt_ itself is easy, its just pulling from git and configuring.

Note: I like to use the location of Apache under /var/www/html. You can obv. put it where ever you wish. Just know that the **web****root should be passbolt/app/webroot**. (this is a _cakephp_ thing)

<pre>mkdir -p /var/www/html/passbolt
cd /var/www/html/passbolt
git clone https://github.com/passbolt/passbolt.git .</pre>

Next change write permissions for the _app/tmp_ and _app/webroot/img/public_ so the _nginx_ user can write files there all the other files need to belong to some user that allows access to the _nginx_ process:

<pre>chmod +w -R /var/www/html/passbolt/app/tmp
chmod +w /var/www/html/passbolt/app/webroot/img/public
chown -R nginx.nginx /var/www/html/passbolt/</pre>

Then copy the *.default files to normal _php_ files;

<pre>cp app/Config/core.php.default app/Config/core.php
cp app/Config/database.php.default app/Config/database.php
cp app/Config/app.php.default app/Config/app.php</pre>

Now we can configure them one by one; In _app/__Config/core.php_ change :

<pre># salt can be any random string
Configure::write('Security.salt', 'put your own salt here');

# cipherSeed should be a random number
Configure::write('Security.cipherSeed', 'put your own cipher seed here');

# https://subdomain.svennd.be
#  - your own obv
Configure::write('App.fullBaseUrl', 'http://{your domain without slash}');</pre>

In _app/__Config/database.php_ change :

<pre># change login (if required)
# change password
# change database (if required)

public $default = array(
  'datasource' =&gt; 'Database/Mysql',
  'persistent' =&gt; false,
  'host' =&gt; 'localhost',
  'login' =&gt; 'root',
  'password' =&gt; 'password',
  'database' =&gt; 'passbolt'
);</pre>

The_ app/Config/app.php _is a bit more difficult, this file is used to link the key and fingerprint to the cakephp application. Since I ran <code class="EnlighterJSRAW" data-enlighter-language="null">gpg --key-gen</code> in the root, some of the files are stored in /root/.gnupng; so to get everything in one place I moved this to <code class="EnlighterJSRAW" data-enlighter-language="null">/opt/passbolt</code> and gave the _nginx_ user access to those files.

<pre>cp -a /root/.gnupg/ /opt/passbolt/
chown -R nginx.nginx /opt/passbolt/.gnupg
chown -R nginx.nginx /opt/passbolt/*</pre>

Then you can change _app/Config/app.php _as follows; Fingerprint is what was generated during key generation, you can look this up using <code class="EnlighterJSRAW" data-enlighter-language="null">gpg --list-keys --fingerprint</code> : (note : you need to remove the spaces in the fingerprint)

<pre>$config = [
    'GPG' =&gt; [
        'env' =&gt; [
            'setenv' =&gt; true,
            'home' =&gt; '/opt/passbolt/.gnupg'
         ],
         'serverKey' =&gt; [
             'fingerprint' =&gt; 'your_fingerprint_value',
             'public'      =&gt; '/opt/passbolt/serverkey.asc',
             'private'     =&gt; '/opt/passbolt/serverkey.private.asc'
         ]
     ]</pre>

Finally we should be able to run the installer :

<pre>su -s /bin/bash -c "app/Console/cake install --no-admin" nginx</pre>

This should result in :

<pre>[root@tetra passbolt]# su -s /bin/bash -c "app/Console/cake install --no-admin" nginx
     ____                  __          ____
    / __ \____  _____ ____/ /_  ____  / / /_
   / /_/ / __ `/ ___/ ___/ __ \/ __ \/ / __/
  / ____/ /_/ (__  |__  ) /_/ / /_/ / / /
 /_/    \__,_/____/____/_.___/\____/_/\__/

 Open source password manager for teams
 (c) 2015-present passbolt.com

---------------------------------------------------------------
 Avatar deleted!
Installing schema / database
---------------------------------------------------------------

Welcome to CakePHP v2.9.4 Console
---------------------------------------------------------------
App : app
Path: /var/www/html/tetra/passbolt/app/
---------------------------------------------------------------
Cake Schema Shell
---------------------------------------------------------------

The following table(s) will be dropped.
roles
users
authentication_tokens
profiles
positions
addresses
phone_numbers
emails
email_queue
category_types
resources
secrets
categories_resources
categories
groups
groups_users
gpgkeys
tags
items_tags
comments
permissions_types
permissions
controller_logs
user_agents
authentication_logs
authentication_blacklists
favorites
schema_migrations
Dropping table(s).
roles updated.
users updated.
authentication_tokens updated.
profiles updated.
positions updated.
addresses updated.
phone_numbers updated.
emails updated.
email_queue updated.
category_types updated.
resources updated.
secrets updated.
categories_resources updated.
categories updated.
groups updated.
groups_users updated.
gpgkeys updated.
tags updated.
items_tags updated.
comments updated.
permissions_types updated.
permissions updated.
controller_logs updated.
user_agents updated.
authentication_logs updated.
authentication_blacklists updated.
favorites updated.
schema_migrations updated.

The following table(s) will be created.
roles
users
authentication_tokens
profiles
positions
addresses
phone_numbers
emails
email_queue
category_types
resources
secrets
categories_resources
categories
groups
groups_users
gpgkeys
tags
items_tags
comments
permissions_types
permissions
controller_logs
user_agents
authentication_logs
authentication_blacklists
favorites
schema_migrations
Creating table(s).
Table roles created
roles updated.
Table users created
users updated.
Table authentication_tokens created
authentication_tokens updated.
Table profiles created
profiles updated.
Table positions created
positions updated.
Table addresses created
addresses updated.
Table phone_numbers created
phone_numbers updated.
Table emails created
emails updated.
Table email_queue created
email_queue updated.
Table category_types created
category_types updated.
Table resources created
resources updated.
Table secrets created
secrets updated.
Table categories_resources created
categories_resources updated.
Table categories created
categories updated.
Table groups created
groups updated.
Table groups_users created
groups_users updated.
Table gpgkeys created
gpgkeys updated.
Table tags created
tags updated.
Table items_tags created
items_tags updated.
Table comments created
comments updated.
Table permissions_types created
permissions_types updated.
Table permissions created
permissions updated.
Table controller_logs created
controller_logs updated.
Table user_agents created
user_agents updated.
Table authentication_logs created
authentication_logs updated.
Table authentication_blacklists created
authentication_blacklists updated.
Table favorites created
favorites updated.
Table schema_migrations created
Execute specific schema PermissionsSchema (ok)
schema_migrations updated.
End create.
passbolt schema deployed

Welcome to CakePHP v2.9.4 Console
---------------------------------------------------------------
App : app
Path: /var/www/html/tetra/passbolt/app/
---------------------------------------------------------------
Cake Schema Shell
---------------------------------------------------------------

The following table(s) will be dropped.
cake_sessions
Dropping table(s).
cake_sessions updated.

The following table(s) will be created.
cake_sessions
Creating table(s).
cake_sessions updated.
End create.
passbolt session table deployed

Welcome to CakePHP v2.9.4 Console
---------------------------------------------------------------
App : app
Path: /var/www/html/tetra/passbolt/app/
---------------------------------------------------------------
Cake Schema Shell
---------------------------------------------------------------

The following table(s) will be dropped.
file_storage
Dropping table(s).
file_storage updated.

The following table(s) will be created.
file_storage
Creating table(s).
file_storage updated.
End create.
plugins schemas deployed

Welcome to CakePHP v2.9.4 Console
---------------------------------------------------------------
App : app
Path: /var/www/html/tetra/passbolt/app/
---------------------------------------------------------------
Installing data set:default
---------------------------------------------------------------
Data for model SchemaMigration inserted (7)
Data for model Role inserted (4)
Data for model User inserted (1)
Data for model Gpgkey inserted (0)
Data for model PermissionType inserted (16)
Data for model Profile inserted (1)
We need you to help make passbolt better by sending anonymous usage statistics. Ok?
(see: https://www.passbolt.com/privacy#statistics) (y/n)
[n] &gt; y

Welcome to CakePHP v2.9.4 Console
---------------------------------------------------------------
App : app
Path: /var/www/html/tetra/passbolt/app/
---------------------------------------------------------------
 Datasource : default
 File :/var/www/html/tetra/passbolt/app/tmp/schema/default_default.sql
 Success: the database was saved on file!
---------------------------------------------------------------

 Passbolt installation success! Enjoy!</pre>

And that's it folks !

  * [installing passbolt on FreeBSD][10]
  * [Install using asciinema on Centos 7.3][11]

 [1]: https://medium.com/passbolt/passbolt-on-centos-7-with-nginx-php7-fpm-mariadb-from-scratch-7b2a9b15f3a4
 [2]: https://fosdem.org/2017/
 [3]: https://fosdem.org/2017/schedule/event/passbolt/
 [4]: https://www.passbolt.com/
 [5]: http://nginx.org/en/linux_packages.html
 [6]: https://www.svennd.be/dehydrated-using-cloudflare-hook/
 [7]: https://www.svennd.be/lets-encrypt-on-any-linux-distro/
 [8]: https://www.svennd.be/lets-encrypt-article-overview/
 [9]: https://www.svennd.be/lets-encrypt-config-for-nginx-1-8-0/
 [10]: https://www.patpro.net/blog/index.php/2016/09/22/3037-self-hosted-password-manager-installing-passbolt-on-freebsd/
 [11]: https://www.svennd.be/passbolt-asciinema-install/
