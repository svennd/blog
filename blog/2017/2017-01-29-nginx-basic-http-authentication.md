---
title: Nginx basic HTTP authentication
author: svennd

date: 2017-01-29T08:21:31+00:00
url: /nginx-basic-http-authentication/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
A short howto, create a _basic HTTP authentication_ on Nginx, Centos 6.8.

##### Requirements

  * Nginx is setup correctly
  * root access

##### Installation

First lets get _htpasswd_, this "manages" the password file and creates users.

<pre>yum install httpd-tools</pre>

&nbsp;

The first run you need to create a new .htpasswd file :

<pre>htpasswd -c /var/www/html/.htpasswd svennd</pre>

This would create the passwd file and a user svennd (you will be prompted for the password)

Next the nginx configuration in the server {} block :

<pre>location /my_secret {
        auth_basic "restricted";
        auth_basic_user_file /var/www/html/.htpasswd;
}</pre>

To restrict access to my_secret directory. Next is to check if nginx configuration is fine :

<pre># nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
</pre>

And reload nginx :

<pre>service nginx reload</pre>

Bam, that easy !