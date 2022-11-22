---
author: "Svenn D'Hert"
title: "rstudio server hack the login"
description: "use Nginx to hack your own rstudio instance"
date: 2022-11-09
thumbnail: /img/2022/05/clay-banks.jpg
url: /rstudio-server-hack/
---
One of the features lacking from the open-source version of rstudio-server  is the possibilty to edit the html pages (eg. to make a custom login page). 
It's highly annoying one can't give atleast some help reference on the login page, so I found a way to "hack" my own webserver by leveraging nginx's [sub_filter](http://nginx.org/en/docs/http/ngx_http_sub_module.html)

You can simply replace the body with something like this :

```
# replace the login page only
location /auth-sign-in 
{
    sub_filter '<body>' '<body> some fancy help message !';
    sub_filter_once on;
    proxy_set_header Accept-Encoding ""; # only required if gzip compression is on
    proxy_pass http://127.0.0.1:8000;
}
```

Using this method one could easily inject javascript or replace the entire page with a custom page.

info : 
- https://support--rstudio-com.netlify.app/products/rstudio/#rstudio-server
- https://stackoverflow.com/questions/19700871/how-to-inject-custom-content-via-nginx


img by [claybanks](https://unsplash.com/@claybanks)