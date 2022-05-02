---
title: how to setup Nginx, php-fpm on Centos 7
author: svennd

date: 2017-03-17T14:56:48+00:00
url: /nginx-php-fpm-centos-7/
thumbnail: /img/2020/04/centos.jpg
categories:
  - Linux
  - SysAdmin

---
The well known, good'ol LAMP stack has slowly made place for a LEMP stack in many of my configurations. Enough articles have been written on [why Apache or Nginx][1] are better in [certain situations][2], but I like the clean way of configuration that _Nginx_ uses. (pronounced [Engine X][3], hence the E in LEMP). So I generally use _Nginx_. I however never made an article about it, so here it is, long overdue.

<!--more-->

##### Install Nginx

There are generally three options to install _Nginx_. First option is to use packages from the vendor, this however leaves you with a older version of _Nginx. _In the base repository's of Centos 7, _Nginx _is not included. So you need to install epel-release if you wish to go that route, at the time of writing, the available package is 1.10.2.This is the fastest option and large chance it will be updated more frequently then when you manually download and compile it. However if you wan't the newest features this is the better option. A third option is to use external repository's. All methods will result in a working setup.

###### 1) Install Nginx using epel

(recommended method)

<pre>yum install epel-release
yum install nginx</pre>

###### 2) Install Nginx from binary packages

Add <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/yum.repos.d/nginx.repo</code>

<pre>[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=0
enabled=1</pre>

Then run <code class="EnlighterJSRAW" data-enlighter-language="null">yum install nginx</code>

_note this installs the [mainline][4]._

###### 3) Install Nginx straight from source

Notice that there is a difference between [mainline and stable version][4]

<pre>cd /opt
mkdir nginx
cd nginx

# check the latest version on : http://nginx.org/en/download.html
wget https://nginx.org/download/nginx-1.11.10.tar.gz

# untar
tar xzvf nginx-1.11.10.tar.gz

# install dependencies
yum install gcc pcre-devel zlib-devel openssl-devel

# configure
./configure --with-http_ssl_module

# make and make install
make
make install</pre>

###### 4) Install Nginx from external repo

You could use webtatic to install nginx; This is similar to how I am going to install php-fpm :

<pre>rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
yum install nginx1w</pre>

for more [detailed instructions, and dynamic modules, see there blogpost][5].

##### Install PHP

Installing PHP (lem**P**) is done using the php-fpm package (PHP FastCGI Process Manager) now sadly in the epel-release the version is super outdated at this writing : PHP 5.4. This version is no longer supported, on top of that, its pretty clear that PHP 7+ is [faster and less resource hungry][6]. Whatever version you chose, either should work. I use [webtatic][7] as repository and have found no issue's so far. For [PHP 7.1][8] : (on Centos 7)

<pre>rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
</pre>

After that you are ready to install PHP :

<pre>yum install php71w-fpm php71w-opcache php71w-mysqlnd php71w-mcrypt php71w-gd php71w-xml</pre>

##### Configure php-fpm

After the installation its time to finetune our setup a bit. Let's start with php-fpm , we need to change <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/php-fpm.d/www.conf</code>

Change both the user and the group to nginx.

<pre>; Start a new pool named 'www'.
[www]

; Unix user/group of processes
; Note: The user is mandatory. If the group is not set, the default user$
;       will be used.
; RPM: apache Choosed to be able to access some dir as httpd
user = nginx
; RPM: Keep a group allowed to write in log dir.
group = nginx


[...]</pre>

_note : it's possible to let Nginx use a socket instead of loopback device. This is [a bit faster][9], but I found that the headache is not really worth it._

##### Configure Nginx

Depending on the installation there is already a default server configuration. I tend to remove it and replace it with an empty file and create a file [name].conf in /etc/nginx/conf.d/

###### http server

<pre>server {
    
    # listen to port 80 
    listen 80;

    # server name or names
    server_name svennd.be;

    # the location of webroot
    # I always use /var/www/html/*
    # Nginx by default uses another structure
    # but this made the transition from Apache allot easier!
    root  /var/www/html/svennd.be;

    # in root location
    location / {
        # look for index.php/index.html/index.htm as "index file"
        index  index.php index.html index.htm;
        
        # this is specifically for wordpress
        # makes it possible to have url rewrites
        try_files $uri $uri/ /index.php?$args;
    }

    # default error pages
    # note that wp already catches most
    error_page 404 /404.html;
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # here we have to let nginx know what to do with these php files
    # as html files are just send directly to the client
    location ~ \.php$ {

        # if the file is not there show a error : mynonexistingpage.php -&gt; 404
        try_files $uri =404;
        
        # pass to the php-fpm server
        fastcgi_pass 127.0.0.1:9000;

        # also for fastcgi try index.php
        fastcgi_index index.php;

        # some tweaking
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param SCRIPT_NAME $fastcgi_script_name;
        fastcgi_buffer_size 128k;
        fastcgi_buffers 256 16k;
        fastcgi_busy_buffers_size 256k;
        fastcgi_temp_file_write_size 256k;

        include fastcgi_params;
    }
}</pre>

Note that [fastcgi_params][10] are installed by php-fpm. In case its missing use the link.

###### https server

Be sure to check [cipherli.st][11] for the latest updates.

<pre>server {
  listen       443 ssl;
  server_name  svennd.be;
  root  /var/www/html/svennd.be;

  ssl_certificate /opt/letsencrypt/certs/svennd.be/fullchain.pem;
  ssl_certificate_key /opt/letsencrypt/certs/svennd.be/privkey.pem;
  # verify chain of trust of OCSP response using Root CA and Intermediate certs
  ssl_trusted_certificate /opt/letsencrypt/certs/svennd.be/chain.pem;

  ssl_session_timeout 1d;
  ssl_session_cache shared:SSL:50m;
  ssl_session_tickets off;

  # Diffie-Hellman parameter for DHE ciphersuites, recommended 2048 bits
  ssl_dhparam /opt/letsencrypt/certs/dhparam.pem;

  # needs more info
  ssl_ecdh_curve secp384r1; # Requires nginx &gt;= 1.1.0

  # suggested conf (interwebz)
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:ECDHE-RSA-AES128-GCM-SHA256:AES256+EECDH:DHE-RSA-AES128-GCM-SHA256:AES256+EDH:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";
  ssl_prefer_server_ciphers on;

  # OCSP Stapling ---
  # fetch OCSP records from URL in ssl_certificate and cache them
  ssl_stapling on;
  ssl_stapling_verify on;

  # need to lookup options
  resolver 8.8.8.8 valid=300s;
  resolver_timeout 5s;

  # headers
  # note HSTS might break your website if initial setup !
  # add_header Strict-Transport-Security "max-age=15768000; includeSubDomains; preload";
  # add_header X-Frame-Options DENY;
  add_header X-Content-Type-Options nosniff;
  add_header X-XSS-Protection "1; mode=block";

  # hide version
  server_tokens off;

    location / {
        index  index.php index.html index.htm;
        try_files $uri $uri/ /index.php?$args;
    }

    error_page 404 /404.html;
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param SCRIPT_NAME $fastcgi_script_name;
        fastcgi_buffer_size 128k;
        fastcgi_buffers 256 16k;
        fastcgi_busy_buffers_size 256k;
        fastcgi_temp_file_write_size 256k;

        include fastcgi_params;
    }
}</pre>

##### Install MariaDB

There are two options to install MariaDB, either from the Centos/RHEL repo's or a newer version from [MariaDB][12]'s repo helper. For simplicity I use the Centos, MariaDB 5.5.52 version. But you can get the 10.1/10.2 if you like.

Install from base repository :

<pre>yum install mariadb-server</pre>

Start the mariadb-server prior to configuration :

<pre>systemctl enable mariadb
systemctl start mariadb</pre>

configure mariadb :

<pre>mysql_secure_installation</pre>

##### Start the server

After that you are ready to run the server :

<pre>systemctl start php-fpm
systemctl enable php-fpm

systemctl start nginx
systemctl enable nginx</pre>

And that's how I generally set up Nginx and php-fpm.

 [1]: https://www.svennd.be/webserver-configuration-for-phpbb-server-on-vps/
 [2]: https://www.digitalocean.com/community/tutorials/apache-vs-nginx-practical-considerations
 [3]: https://en.wikipedia.org/wiki/Nginx
 [4]: https://www.svennd.be/nginx-stable-is-not-so-mainline/
 [5]: https://webtatic.com/packages/nginx110/
 [6]: https://www.quora.com/What-are-the-major-difference-between-PHP-5-and-PHP-7
 [7]: https://webtatic.com
 [8]: https://webtatic.com/packages/php71/
 [9]: http://stackoverflow.com/questions/14973942/performance-tcp-loopback-connection-vs-unix-domain-socket
 [10]: http://pastebin.com/W2ZmtNTa
 [11]: https://cipherlist.eu/
 [12]: https://downloads.mariadb.org/mariadb/repositories/#mirror=cube&distro=CentOS&distro_release=centos7-amd64--centos7&version=10.1