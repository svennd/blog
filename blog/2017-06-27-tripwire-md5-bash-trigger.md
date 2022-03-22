---
title: Tripwire md5 bash trigger
author: svennd

date: 2017-06-27T14:52:04+00:00
url: /tripwire-md5-bash-trigger/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - tripwire

---
After reading [cron.weekly][1] a few weeks ago, I was intrigued by [binsnitch.py][2], a tool that creates a baseline file with the md5/sha256/... hash of every file you wish to monitor. In case you think you have a virus, malware or cryptovirus you can verify easely what files have been changed. This is kinda fun, the sad part is, it uses Python, and requires python >= 3 which restricts the use on Centos (python 2 default). I dislike a unneeded dependency like that on my servers. So I wrote a quick and dirty alternative to it. Only requirements are bash and md5sum (or if you wish some other sum tool such as sha256sum) which I believe are common on every Linux server.

You can download & adapt it [here][3].

 [1]: https://www.cronweekly.com/issue-81/
 [2]: https://blog.nviso.be/2017/05/17/using-binsnitch-py-to-detect-files-touched-by-malware/?utm_source=cronweekly.com
 [3]: https://github.com/svennd/tripwire_bash