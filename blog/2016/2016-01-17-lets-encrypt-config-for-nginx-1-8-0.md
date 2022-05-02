---
title: Lets encrypt config for nginx 1.8.0
author: svennd

date: 2016-01-17T21:10:15+00:00
url: /lets-encrypt-config-for-nginx-1-8-0/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I already made [Lets Encrypt working with Centos 6.7][1] and Apache, recently I tried out Nginx, I wasn't blasted with the speed, but I do like the way the config is made. The fact that it should be faster is icing on the cake. ([some simple benchmark showed at-least a little bit better][2]) So I switched over -I'm no expert on Nginx- and below I posted my current config for ssl on nginx (version 1.8.0)

This was part of the server block :

<pre>listen 443 ssl;

server_name  svennd.be;
ssl_certificate /etc/letsencrypt/live/svennd.be/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/svennd.be/privkey.pem;

# ssl session caching
ssl_session_timeout 1d;
ssl_session_cache shared:SSL:10m;

# openssl dhparam -out dhparam.pem 2048
ssl_dhparam /etc/nginx/cert/dhparam.pem;

ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!3DES:!MD5:!PSK';
ssl_prefer_server_ciphers on;

add_header Strict-Transport-Security max-age=15768000;

ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 8.8.4.4 valid=86400;
resolver_timeout 10;</pre>

Note that I had some "tricks" to make the penalty on ssl a little bit smaller. I'm also planning on posting my full nginx config, but that still needs some more work. I'd like to explain a bit why I choice these values.

Don't forget : Encrypt the web!

  <img aria-describedby="caption-attachment-920" loading="lazy" class="wp-image-920 size-full" src="/img//2015/07/25446736-1.jpg" width="339" height="81" srcset="/img/2015/07/25446736-1.jpg 339w, /img/2015/07/25446736-1-300x72.jpg 300w, /img/2015/07/25446736-1-4x1.jpg 4w" sizes="(max-width: 339px) 100vw, 339px" />


 [1]: https://www.svennd.be/lets-encrypt-with-centos-6-7/
 [2]: https://www.svennd.be/webserver-configuration-for-phpbb-server-on-vps/
