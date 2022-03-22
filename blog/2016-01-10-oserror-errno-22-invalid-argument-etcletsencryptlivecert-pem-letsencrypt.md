---
title: 'OSError: [Errno 22] Invalid argument: ‘/etc/letsencrypt/live/cert.pem’ letsencrypt'
author: svennd

date: 2016-01-10T19:46:12+00:00
url: /oserror-errno-22-invalid-argument-etcletsencryptlivecert-pem-letsencrypt/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I recently switched from Apache to Nginx and also to a new server. Yeey! I copy'd <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/letsencrypt/</code> over from the first server to the second. Everything seemed to be fine. Sadly, nope! For some reason it doesn't accept certificates made on another server. Fixing it is easy once you find it :

<pre># remake / save your config file
nano /etc/letsencrypt/cli.ini

# remove all info on letsencrypt
rm -rf /etc/letsencrypt/

# remake cert's
/opt/letsencrypt/letsencrypt-auto --config /etc/letsencrypt/cli.ini --debug certonly

# restart server might be needed
service nginx restart</pre>

Happy encrypting 🙂