---
title: putty inside notepad++
author: svennd

date: 2015-07-14T19:52:43+00:00
url: /putty-inside-notepad/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin
tags:
  - notepad++
  - putty
  - ssh
  - windows

---
I'm a huge fan of notepad++ , while I would love to see a Linux version, for now its only limited to Windows and wine installations. Since I work with Linux pretty much all day and use my local machine as a [thin client][1] (hp elitebook 8530p). Its super useful to be able to run putty from inside notepad++. While I found a way a while ago, I still am not very happy with the result, even so that I dropped it again and when to the two windows again. However, I still like to share the way I have found before.

&nbsp;

  1. Install a plugin that runs commands <del>NppExec</del> or [NppConsole][2]
  2. download [plink][3]
  3. download [ansicon][4] (or anything else that can "translate" linux output to windows)
  4. move these executable to somewhere where the PATH variable goes looking, or add the location to the PATH variable . ([tutorial][5])
  5. Restart np++
  6. run the command (F6) <code class="EnlighterJSRAW" data-enlighter-language="null">ansicon.exe -p plink.exe -ssh user@location</code>
  7. buy me a beer.

![this is the console you get with NppExec ](/img/2015/07/npp_exec-1024x831.png)

![this is the console you get with NppConsole](/img/2015/07/Inkednppconsole_LI-1024x824.jpg)

 [1]: https://www.google.be/search?q=elitebook+8530p
 [2]: http://sourceforge.net/projects/nppconsole
 [3]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
 [4]: https://github.com/adoxa/ansicon
 [5]: https://www.java.com/en/download/help/path.xml
