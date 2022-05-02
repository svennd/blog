---
title: Todo list on Ransomware attack
author: svennd

date: 2016-11-25T15:19:42+00:00
url: /todo-list-on-ransomware-attack/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin

---
At work we been hit with a ransomware attack, this was a new experience for me. Lucky it was a "good" one, as we had backups and we where very fast to stop outbreak. However finding what to do, seemed more difficult then one would expect. For starters pulling out all the plugs is easy. Then comes the hard part, finding out what is happening, whats it's going to do once you start again, how to get rid of it, and then trying to find ways to stop it from happening again, or at best create the least possible amount of damage. While this new hype is growing every day, finding decent up-to-date information seems not as trivial.  So for future reference, or for someone searching for quick information here goes :

**Stop the encryption  
** - the moment you discover some **computer** in the network is doing encryption, is to **stop it**, remove the network, remove the power.  (this computer is going to be build up from scratch so don't worry about the work, its basically lost)  
- If you have a **backup system** in place **stop** it and **deconnect** it from **network**. This is important cause you don't want to corrupt your backup.  
- shut down any **computer** you think might be **infected,** or **damaged**

That was the easy part, shutting everything down. Now people really will start freaking out.

**Find out how big the damage is  
** - **boot** in **a system** you know is **secure**, read-only systems are great for this (Chrome OS, live USB, ...) and use that to research  
- **determ** **the** type of **ransomware**; and any specs you can find. In case of doubt, this would be an ideal time to contact a specialist. ([this can help][1]) This is also the time you search for a solution or a plan off "recovery". In our specific case the ransomware was acting from one computer, and all other computers where affected but not infected. So we booted all the machines up (except for the infected) and kept a close monitor on new malicious activity, luckily for us nothing showed any weird behavior.

**Recovery  
** - We started **put**ting **back** **backups.  
** - Since we did not trust the infected machine, we **removed** the **disks** and **reinstalled from scratch** and put a backup of the data back.

All that was lost was time, hopefully this post can be archived never to be used again, but I fear so.

Some useful links :

  * [prevent ransomware with windows module][2]
  * [techtarget's take on ransomware][3]

 [1]: https://www.nomoreransom.org
 [2]: http://woshub.com/using-fsrm-on-windows-file-server-to-prevent-ransomware/
 [3]: http://searchsecurity.techtarget.com/news/450304898/FBI-ransomware-alert-Dont-pay-report-defend-against-attacks