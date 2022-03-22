---
title: a client request body is buffered to a temporary file
author: svennd

date: 2016-11-13T15:05:42+00:00
url: /a-client-request-body-is-buffered-to-a-temporary-file/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
  - webdev

---
A little cute harmless nginx **[WARNING]** yea, one of those. It means an uploaded file was larger then the allowed buffer (in memory) set by **[client\_body\_buffer_size][1] **which by default is 16kb (on x64 bit). So for any upload of image, you can expect this to be allot larger. (images can easely be 3-6mb a piece)  I decided to increase the value to 1mb, meaning most images will still fire this warning, but smaller images (web-ready) won't. I also changed **client\_max\_body_size** which sets a maximum upload size (an http error 413 would be returned when attempted a larger upload)

<pre>client_body_buffer_size 1M;
client_max_body_size 10M;</pre>

So this warning is just a notice, that a write has been written to file system, which is a bit slower then to memory. (a lot slower) When you see allot of these popping up, it might be a good idea to increase this value, to not over use your hard drive / ssd, but at the cost of reserved memory. (RAM)

 [1]: http://nginx.org/en/docs/http/ngx_http_core_module.html#client_body_buffer_size