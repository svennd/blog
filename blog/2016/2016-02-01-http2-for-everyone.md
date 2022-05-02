---
title: HTTP/2 for everyone
author: svennd

date: 2016-02-01T20:46:28+00:00
url: /http2-for-everyone/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
After reading the [slides of ma.ttias.be on http/2][1] I feel more interesses in the topic of http/2. While the presentation is aimed at PHP developers, I think most Linux users/sysadmins can pick something up of the presentation. I'm a bit ashamed I did not already knew about this, but hey, we learn everyday. Http/2 seems to be an enhancement of http/1.1 and certainly an upgrade on http/1. While i am clearly no expert on the topic, it goes like this :

  * http/1 : header and TCP handshake are send for every request (request is : index / style.css / jquery.js / ...) There was an addition of _keepalive_ to the protocol but it is not on by default
  * http/1.1 :  _keepalive_ is on per default unless defined otherwise, now multiple requests can happen in one handshake. Although still restricted to 6-8 connections per domain. (meaning you can download 6-8 items at a time once the page is parsed)
  * http/2 : keepalive is on by default and server can now send multiple files concurrent at once per connection line, headers are compressed saving space. Serverside pushes can happen (this is freaking awesome if working, one could send a css file while the page is not yet parsed by the client)

Be sure to check out ma.ttias.be's his slides those are really a good explanation :

_[Slides: HTTP/2 for PHP developers  
][2]_ 

All (?) browsers supporting http/2 also require https, but we know where to find that : [lets encrypt][3]. Although short, might be a good read for [http/2 on wikipedia, here][4].

 [1]: https://ma.ttias.be/slides-http2-for-php-developers/
 [2]: https://ma.ttias.be/slides-http2-for-php-developers/#comment-26982
 [3]: https://letsencrypt.org/
 [4]: https://en.wikipedia.org/wiki/HTTP_persistent_connection