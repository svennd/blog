---
title: 'Fix Nginx 413 : Request Entity Too Large'
author: svennd

date: 2017-08-07T09:52:30+00:00
url: /fix-nginx-413-request-entity-too-large/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - nginx

---
It's not the first time I received this error. It is the error you receive when the file (or large input in general) you tried to upload is too large and the server is declining it. Here is how you can fix it :

Open your _server {}_ specific config file for nginx :

<pre>nano /etc/nginx/conf.d/svennd.conf</pre>

and add, 10M is ~10MB.

<pre>client_max_body_size        10M;
</pre>

Note that PHP also has an upload limit ([see the docs][1]).

Reload Nginx to activate :

<pre>service nginx reload

or 

systemctl reload nginx</pre>

note that _clent\_max\_body_size_ can be in both http and server context, I prefer server as it is more specific.

Alternative error :

<pre>client intended to send too large body</pre>

&nbsp;

 [1]: http://php.net/manual/en/features.file-upload.common-pitfalls.php