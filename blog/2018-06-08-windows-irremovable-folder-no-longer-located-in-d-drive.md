---
title: 'Windows : irremovable folder, no longer located in D drive'
author: svennd

date: 2018-06-08T08:30:33+00:00
url: /windows-irremovable-folder-no-longer-located-in-d-drive/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin
  - windows
tags:
  - windows

---
I could not remove this folder in _Windows. _It gave an error that is was not there anymore ... uh-oh ... corruption ? I checked the disk using the windows utility but the disk seemed fine (raid was a-OK).

To my surprise the only solution was to go to the windows console / command prompt and remove the directory directly from there. So Windows is becoming more like Linux, I like it !

The command to be used is [_rd_][1] :

<pre>rd /S \\?\D:\arch_micro_data\svennd\data\2015</pre>

_rd_ stands for _remove directory_, the /S is the flag for recursively remove the tree.

After running this command, and accepting the removal, bloop, directory was gone.

What the **\\?** means I have not found, but the solution came from [spiceworks][2].

 [1]: https://ss64.com/nt/rd.html
 [2]: https://community.spiceworks.com/topic/1529689-this-folder-no-longer-located-in-d-but-it-is