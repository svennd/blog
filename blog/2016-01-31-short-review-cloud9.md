---
title: 'short review : Cloud9'
author: svennd

date: 2016-01-31T12:31:28+00:00
url: /short-review-cloud9/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - review
  - webdev

---
In my free time I play some games, as such I don't have Linux on my personal computer(s), for work, I just ssh into any machine and can do whatever there. However I wanted to create some code as hobby project, and unlike other times, I didn't wanne use SVN "sync" method. I work on 3 laptops and 1 desktop, so its useful to have one central place where my code base is. On top of that, I like to store every mini edit. I can't commit like normal people, normal people finish one item then commit, I commit every change and try them out, even if a feature is not completed.

This makes complex commits very annoying, in SVN I just commit every change and my test server pulls all changes every second, So my develop machine and "write" machine are separate, that is great if I switch machine.

**X2Go**

Anyway, I set up a private repo on github and tried x2go on my desktop at work. X2Go is an alternative way of X over ssh which works super, super fast. (compared to X over ssh) Sadly there is no good code editor installed, and I tried to install atom, but that just would not start over X or X2go ... (no idea why). Its also annoying that full screen in x2go doesn't show the mouse (bug?)... so this Idea was rather quickly abandoned.

**Cloud9 // c9.io**

Hence I googled what ways other people attack this sort of problems... and Cloud9 came in to view.  They offer a free account, even private "project", ssh like webconsole, -I like-. I went ahead and created an account, I could make one using github so I went to pick that option, I checked/accepted/next until I hit a login box, this did know my username, but sadly it did not let me login ... >.<  After trying to reset the password and activation mail, I just made an attempt to create a new account. That did work, so to the other svennd on cloud9 I'm sorry for resetting your password.

**Getting the code to cloud9**

The main advantage is, once the code is there no matter where I code, I have the same base & test environment. That being said, getting it there was a bit confusion, but very easy because of the console. I have a private git repo, so I need to give my password, the initial attempt was silently failing. So using the console, I just used <code class="EnlighterJSRAW" data-enlighter-language="null">git pull</code> the codebase was there directly. Opening files is easy and you even see the file tree.

**Next up : running the code**

Now, onto the writing and testing the code in one place. I started a MySQL entity and created my database and tables; This went as one would expect on the console although I did experience a bit of "lag" considering this was 15 seconds after registration, I found it very fast overall. (much faster then installing a container/vps) Next was firing up Apache with mod_php, this was like 1 click away. Again :  awesome. I got a link pointing me to https://c9 website. Great, lets try the app. My app worked until I tried to add stuff to the database, for some reason that did not work, highly likely my code is to blame however debugging was kinda hard as I did not find the logs of apache ... This is where I got bored and jumped ship. One thing I highly appreciate in application is the possibility to remove ones code and remove the account totally.  Both where done rather fast. Crazy enough this convinced me to give cloud9 a better try with a more patience.

Some wins for cloud9

  * Speedy : from codebase to testing, in minutes. (php/mysql/apache)
  * As far as I tested : some customization options in editor (I like light colors, not black)
  * github implementation

Some missers for cloud9

  * Missing some tutorial/intro.
  * Debugging might not be so easy
  * Online only (?)

This review is based on 30 minutes of playing with [Cloud9][1]. This is rather premature!

  ![cloud9, definitely worth looking in to.](/img//2015/07/25450800-1.png)


 [1]: https://c9.io
