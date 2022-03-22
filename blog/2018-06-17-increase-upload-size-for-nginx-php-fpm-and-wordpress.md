---
title: Increase upload size for Nginx, php-fpm and WordPress
author: svennd

date: 2018-06-17T13:49:47+00:00
url: /increase-upload-size-for-nginx-php-fpm-and-wordpress/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - nginx
  - wordpress

---
In order to increase the size of files you can upload in WordPress, we need to adapt the software powering the software.

Let's begin with PHP, I use Nginx + php-fpm to run PHP, so I need to edit

<pre>/etc/php.ini</pre>

on line 799, _upload\_max\_filesize _determines the maximum allowed size a PHP file will accept for upload.

<pre>upload_max_filesize = 2M</pre>

This should not be to big, but a few megabytes will work just fine. I increased this to 10M (~10MB)

After that its time to update Nginx to [allow larger upload body's][1], this I already had an error documented in the past. So in short :

<pre>/etc/nginx/nginx.conf</pre>

add after the

<pre>http {

# increase upload size to 10MB
client_max_body_size 10M;</pre>

Once that is done, just restart Nginx and php-fpm.

<pre>systemctl restart nginx
systemctl restart php-fpm</pre>

And voila

[![](/img/2018/06/upload_wordpress.png)][2]

 [1]: https://www.svennd.be/fix-nginx-413-request-entity-too-large/
 [2]: /img/2018/06/upload_wordpress.png