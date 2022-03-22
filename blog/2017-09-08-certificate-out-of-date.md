---
title: certificate out-of-date
author: svennd

date: 2017-09-08T16:38:30+00:00
url: /certificate-out-of-date/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin
tags:
  - certificate

---
That awkward moment when you write so much articles to help people automate and get certificates and then don't correctly validate that my server is working correctly ... 😀 Sorry for the downtime !

What happened is : I host 3 domains on 1 server, one of those 3 is no longer used, however the script still tries to ask a certificate, this resulted in an error, (since i run this script weekly for quit some weeks now). This results in the nginx server not taking the new certificates. That still leaves me weeks to find out, however I did not find it out. Until today, when I'm on holiday (horrible connection). On top of that I find that cloudflare's "always on" is in fact : free advertisement for cloudflare's broken function. (my website was not cashed at all !) and visitors could not even accept the out-of-time certificate.

Well it's now hotfixed. Enjoy my blog once more ! And I hope I can enjoy the rest of my holidays !