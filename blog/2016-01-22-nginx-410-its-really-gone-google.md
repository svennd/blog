---
title: nginx 410, its really gone google.
author: svennd

date: 2016-01-22T21:48:48+00:00
url: /nginx-410-its-really-gone-google/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Googlebot is a real hero, it believes my ancient posts are still not dead. In webmaster tools I still get the message I got 404's on my website, linking to ancient posts I made a few years back. As long as someone remembers they are not dead! -Keep believing that-  Anyways, while 404 don't really hurt the website -web is dynamic-, its a clean way to report back that there is little to no chance those exact posts will ever return.

One can simply add [410][1]'s to the Nginx configuration to show bots and browsers that the page is removed from the website/database and should not be accessed any longer. I added them manually to the location / with this ugly little hack :

<pre>location / {
if ($uri ~ "/working-on-server-move/") { return 410; }</pre>

if you try something similar first try with a [418 (htcpcp)][2], see a 418 at work.

 [1]: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes
 [2]: https://en.wikipedia.org/wiki/Hyper_Text_Coffee_Pot_Control_Protocol