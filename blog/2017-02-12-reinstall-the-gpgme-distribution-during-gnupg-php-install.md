---
title: Reinstall the gpgme distribution during GnuPG PHP install
author: svennd

date: 2017-02-12T12:44:37+00:00
url: /reinstall-the-gpgme-distribution-during-gnupg-php-install/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
Trying to install [GnuPG][1] and failing with :

<pre>configure: error: Please reinstall the gpgme distribution
ERROR: `/var/tmp/gnupg/configure --with-php-config=/usr/bin/php-config' failed</pre>

I hit on this error while installing using _pecl_ :

<pre>[root@tetra tetra]# pecl install gnupg
downloading gnupg-1.4.0.tgz ...
Starting to download gnupg-1.4.0.tgz (28,349 bytes)
.........done: 28,349 bytes
6 source files, building
running: phpize
Configuring for:
PHP Api Version:         20151012
Zend Module Api No:      20151012
Zend Extension Api No:   320151012
building in /var/tmp/pear-build-root7FLwJg/gnupg-1.4.0
running: /var/tmp/gnupg/configure --with-php-config=/usr/bin/php-config
checking for grep that handles long lines and -e... /usr/bin/grep
checking for egrep... /usr/bin/grep -E
checking for a sed that does not truncate output... /usr/bin/sed
checking for cc... cc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables...
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether cc accepts -g... yes
checking for cc option to accept ISO C89... none needed
checking how to run the C preprocessor... cc -E
checking for icc... no
checking for suncc... no
checking whether cc understands -c and -o together... yes
checking for system library directory... lib
checking if compiler supports -R... no
checking if compiler supports -Wl,-rpath,... yes
checking build system type... x86_64-unknown-linux-gnu
checking host system type... x86_64-unknown-linux-gnu
checking target system type... x86_64-unknown-linux-gnu
checking for PHP prefix... /usr
checking for PHP includes... -I/usr/include/php -I/usr/include/php/main -I/usr/include/php/TSRM -I/usr/include/php/Zend -I/usr/include/php/ext -I/usr/include/php/ext/date/lib
checking for PHP extension directory... /usr/lib64/php/modules
checking for PHP installed headers prefix... /usr/include/php
checking if debug is enabled... no
checking if zts is enabled... no
checking for re2c... no
configure: WARNING: You will need re2c 0.13.4 or later if you want to regenerate PHP parsers.
checking for gawk... gawk
checking for gnupg support... yes, shared
checking for gnupg files in default path... not found
configure: error: Please reinstall the gpgme distribution
ERROR: `/var/tmp/gnupg/configure --with-php-config=/usr/bin/php-config' failed</pre>

What it is missing is some development files that gpgme supply; On Centos this is the dependency that is missing :

<pre>yum install gpgme-devel</pre>

On Debian :

<pre>apt-get install libgpgme11-dev</pre>

After that, it installed perfectly, don't forget to add a file /etc/php.d/ (on Centos) with content :

<pre>#load gnupg extension
extension=gnupg.so</pre>

&nbsp;

 [1]: https://www.gnupg.org/