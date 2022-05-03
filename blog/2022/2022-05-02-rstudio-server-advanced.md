---
author: "Svenn D'Hert"
title: "R-Studio server setup"
description: "Going 'pro', on opensource R-Studio server edition"
date: 2022-05-01
thumbnail: /img/2022/05/clay-banks.jpg
url: /rstudio-advanced/
---

config in nginx
```
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        server_name _;
        return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name cmn-rira.bioinf.be;

  ssl_certificate /opt/dehydrated/certs/cmn-rira.bioinf.be/fullchain.pem;
  ssl_certificate_key /opt/dehydrated/certs/cmn-rira.bioinf.be/privkey.pem;
  # verify chain of trust of OCSP response using Root CA and Intermediate certs
  ssl_trusted_certificate /opt/dehydrated/certs/cmn-rira.bioinf.be/chain.pem;


ssl_protocols TLSv1.3;# Requires nginx >= 1.13.0 else use TLSv1.2
ssl_prefer_server_ciphers on;
ssl_dhparam /opt/dehydrated/dhparams.pem; # openssl dhparam -out /etc/nginx/dhparam.pem 4096
ssl_ciphers EECDH+AESGCM:EDH+AESGCM;
ssl_ecdh_curve secp384r1; # Requires nginx >= 1.1.0
ssl_session_timeout  10m;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off; # Requires nginx >= 1.5.9
ssl_stapling on; # Requires nginx >= 1.3.7
ssl_stapling_verify on; # Requires nginx => 1.3.7
#resolver $DNS-IP-1 $DNS-IP-2 valid=300s;
resolver_timeout 5s;
#add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
add_header X-Frame-Options DENY;
add_header X-Content-Type-Options nosniff;
add_header X-XSS-Protection "1; mode=block";

    location / {
       proxy_pass http://127.0.0.1:2805;
    }
}
```


img by [claybanks](https://unsplash.com/@claybanks)
