---
title: rethinking dynamic/static pages
author: svennd

date: 2016-01-28T20:39:55+00:00
url: /rethinking-dynamicstatic-pages/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - webdev

---
I always wondered why there are so many dynamic websites out there with static content. Now that JavaScript has moved out of the "click/counter/funny mouse effect" field and in to a real scripting language ([in case you missed it, get out of under that rock][1]). I might explain what I mean by using this image :

  [![custom vs dynamic ](/img//2015/07/25436088-1.png)](http://www.bbc.co.uk/education/guides/znkqn39/revision)

**Static pages**, are (generally) html files a server just can grab and give to the client. That doesn't mean they can't change, but generally it means the server doesn't have to "think" on them when the client requests them. That is the reason static is super blazing fast and its great when your website is small and static of content. If its however larger and you wanne change something, static isn't that great, since you need to change every file you made up to that point and change them.

The larger part of the web (its a guess, I don't have numbers) however, uses **dynamic pages**, those are code/scripts written in a scripting/programming language such as PHP (WordPress for example), ASP, Java, Perl, Python, Ruby, ... There the process goes different, when a user requests a page, the server has to proces the page, execute it, most of the time request something from the database and then "complete" the page and give it to the client. This is, as you expected, a bit more work. Although since computers these days are fast, most of the time, nobody notices.

Until you are running on a not so new/powerful server, have allot of users, have bad code or just a combination. Exactly what hosting companies do, they put allot of clients in one box, cause the CPU's are only used when someone visits, so if 10 websites get 1 visitor each, its lightning fast. If those 10 get a 100 visitors, suddenly its not that fast anymore. This is commonly known as shared hosting and for allot of projects that is oké, a step up from that would be a VPS, where you get a piece of hardware dedicated to you, but still you share, and hence the larger you get the slower it gets. Then there is dedicated servers, but again, at a certain point you would outgrow them too. Since a computer can only execute so many requests at a time.

Now smart engineers came up with something called **caching**, which generally mean, if we have to calculate 1+2 every second for 100 users, why not just keep 3 and return that ? So generally caching keeps the result of a request in memory when someone else asks, it can simply return the answer. The thing is, the first one will still have to wait for everything, only when someone else asks the same thing will it be fast, again if you have allot of content, most of the time you would have to wait. Anyway caching can happen on allot of levels and it has proven its value. That however got me to think, why do we even need dynamic pages if we are going to cache everything anyway ?

For blogs for examples, at time of creation all data and all information is known by the database and by the logic... so why would a visitor have to wait to redo that every time, while, generally one could just do all the work before a visitor get here and just return the static ? Hence come the [static generators][2]. Those utilize the power of dynamic website, but instead of doing the same thing for every visitor, they just "precompile" the website and the user only has to download static files hence, servers are relieved 🙂

Now I searched a few of these static generators, and generally I haven't found what I was looking for, most of those that I found are black and white, they separate the dynamic from the static in that way that there is no dynamic anymore at all. Why ? I have no answer. Combine the power of the two ... WordPress for example has a super powerful dashboard, a WYSIWYG editor (including a media manager,....) compared to markup commonly used for readme files used by a few of those static generators. I don't get it. Why not put a database behind a static blog for search queries, instead of using 3the party search engines -if at all- ? Why not generate the pages when they are requested for large blogs, that way they could change design w/o remaking all the static pages, per direct (offload them). I would think that a hybrid dynamic system can combine the power of both, and in most cases is way better then static or dynamic.

In short I'm thinking of building my own blog system. A core that is dynamic and can accept comments, that get validated by askimet, then get rendered in the html page. if no html page is available redirect the request to a rendering page. (or if no content is found 404). All technology is there, I don't see the big loophole in my plan.

Options :

  * - html page rendered during creation
  * - html page re-rendered during comments
  * - non existing html page : render at client request if not finished by cron yet
  * - non existing page : 404

In fact I think backend should be fully dynamic, but the frontend should be static html files, rendered by the backend during creation.

The hybrid blog, with all the fun of dynamic pages. #mydreamcontinues

 [1]: https://en.wikipedia.org/wiki/Category:JavaScript_libraries
 [2]: https://www.staticgen.com
