---
title: Favicon compression
author: svennd

date: 2015-08-23T17:05:53+00:00
url: /favicon-compression/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin

---
While checking speed using [webpagetest.org](http://www.webpagetest.org/" target="_blank" rel="noopener noreferrer), I noticed the favicon I created was 97,3 kB large. Now that "only" contributes to 386ms load time (62ms initial, 72ms ttfb and 252ms content download).  I thought that was pretty oké, until I checked out yet another service that checks common speed issues. (zoompf) Generally I distrust commercial products as they always claim to make your server run 10x as fast if you just buy there product.  Similar numbers popped out of the free result : The favicon could be compressed and up to 91% "faster".

So I googled around and found a nice article, but sadly its for Mac claiming [up to 60% smaller file size without sacrificing quality][1]. Lucky for my wallet, I don't own a Mac and the suggested tools don't work with windows all that well. So I tried [favicon.cc](http://www.favicon.cc" target="_blank" rel="noopener noreferrer) to upload and "auto create" an ico. The result was acceptable and the download of my favicon was only 1,12 kB large anymore !

Now I started jumping around. With some more googling however, I found that the original large .ico is in fact a container for multiple images (in my case 5) that will be picked by the browser depending on what the browser likes best. ([cheat sheet on favicons](https://github.com/audreyr/favicon-cheat-sheet" target="_blank" rel="noopener noreferrer))

So back to zero ? Not really, in my hunt to find a service that could make compressed ico's for me I found [favicomatic][2]. A great service that picks up my original png file (5,64 kB) and makes all kinds of formats. (including the code I have to copy & paste!)

[table]  
file,size  
apple-touch-icon-57x57.png,4kb  
apple-touch-icon-72x72.png,4kb  
apple-touch-icon-114x114.png,10kb  
apple-touch-icon-120x120.png,6kb  
apple-touch-icon-144x144.png,8kb  
apple-touch-icon-152x152.png,15kb  
favicon.ico,6kb  
favicon-16x16.png, 1kb  
favicon-32x32.png, 2kb  
mstile-144x144.png, 8kb[/table]

While allot more then the favicon.cc's 1,12 kB still a reduction from 97,3 kB (_note: not all of them get downloaded, just the one the browser likes best_)!

Now kB's don't say much when you don't know how much ms's I just shaved off.

[table]  
type,before,after  
download,97.6 KB, 0.8 KB  
time,252 ms, -  
[/table]  
Using webpagetest.org, who selected favicon-16x16.png. As seen in the table, the ms is not given, considering (97,6/252) x 0,8 =~ 0.3 ms it seems that the download time is trivial <1ms and as such is not given. (from the Brussels datacenter of webpagetest, the website is located on an Amsterdam datacenter)

**Discussion**

Favicons are the tiny little things in the browser, that get loaded when you visit a site, they also show up in front of the bookmark. So its useful to set up a nice one. While digging I found that the ico I used was rather large, and that is not really needed. So I down scaled it using different services, of which I liked favicomatic.com the most.

While checking how much speed I had gained, I noticed that [google's builtin development tools (control-shift-I) din't show favicon.ico][3]. So I  cannot reliably say how much speed I have gained, if I even have gained speed, as ico's generally get downloaded only once, and I had to add html code to support multiple files for Apple devices and w10's edge browser.

The webpagetest.org however gives me a better score and in no way 3 lines of html equal 97kB of ico. So I think I made some gain, I doubt users will notice the 250ms difference, but on a bad server day it might make some difference ! The key to a faster website obviously is in tiny steps!

 [1]: http://jaydenseric.com/blog/favicon-optimization
 [2]: http://www.favicomatic.com
 [3]: https://code.google.com/p/chromium/issues/detail?id=110449
