---
title: 'Useful Linux command : watch'
author: svennd

date: 2015-07-13T15:41:30+00:00
url: /linux-watch/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
tags:
  - command
  - console
  - watch

---
**watch**

> Watch runs command repeatedly, displaying its output. This allows you to watch the program output change over time.  - man page

Sounds a bit cryptically right ? lets use an example to show how fun this command is !

<!--more-->

**watch svn up**

Would update SVN repo in the pwd (working directory)  every 2 seconds! One could ask how useful that really is, well, I use SVN for "syncing" my data. So unlike normal repo's I create an commit every 5 minutes. (with typo's every few seconds) so its easy to just let the server check if I already committed stuff. Now this is just basic usage. It has a few useful features :

  * -n [interval] : use when you don't need every 2 seconds, for example -n 5 would update every 5 seconds. The minimum seems to be 0.1, thought for this I think you better should look to a bash loop or daemons.
  * -d  : checks the difference between the latest output and the new one (-differences)
  * -differences=cumulative : checks the difference between the first output and the latest
  * -c : show console color for example using ls -color (-color)

Another neat trick is combining commands using _**&&**_ this way we can do multiple commands at once.

watch  "svn up && rm -rf cache/*"

_note, the quotes are needed, else it will do the first command and when watch would stop (never) execute the second command._ 

Can you guess what would happen here ? Every 2 seconds an _svn up_ and a clean of the cache 🙂 automation just got easy!_ _Now lets add differences, there is only one option, cumulative or not. Cumulative will take the first output and compare it until stopped, the 'normal' version will check only the difference between the current and the one just gone. (diff n-1, n)

watch -d -c -n 1 "cat test && echo 'lol' >> test"

![write a string (lol) every second to a file, and use watch build-in diff to show the difference.](/img//2015/07/29731880-1.png) 

watch -differences=cumulative -c -n 1 "cat test && echo 'lol' >> test"

A more useful function could be to check if you're application is going to OOM (out of memory), for example using :

watch -d -n 1 free -m

While it might not be a daily used command, it can help you monitor applications better. Have fun watch'ng ! Not in the creepy way!

&nbsp;