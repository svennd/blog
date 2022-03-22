---
title: history command in bash, Linux
author: svennd

date: 2016-01-17T19:10:45+00:00
url: /history-command-in-bash-linux/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
This is one of those commands every sysadmin should read up on the manpage. I use history on a daily basis and recently I bumped onto a article with a nice find. Although I never stopped to think this was supposed to be out there : removing/filtering stuff from history. If you don't know the command history, this is how it looks (part)

<pre># history
  988  php -v
  989  which php
  990  ll /usr/bin/php
  991  ls -l /usr/bin/php
  992  cat /var/log/messages
  993  iptables -L
  994  exit
  995  iptables -L -n
  996  iptables-save</pre>

While generally I prefer to have close to infinite history in Linux, when you just entered :

<pre>mysql -u root -pMYFANCYDIFFICULTPASSWORD main_database</pre>

You start to think, jee I hope nobody checks my history here... or

<pre>rm -rf *</pre>

I hope nobody just copy's this in the root... -I know its a bad habit, don't judge-. On the other hand I type <code class="EnlighterJSRAW" data-enlighter-language="null">ls, ll, pwd, cd,</code> about 15 times/minute when logged in to a console (I'm that sort of crazy). That is not really useful information to store.

So I was very happy to learn these new tricks with <code class="EnlighterJSRAW" data-enlighter-language="null">history</code> command in mind. First thing to do, is increase the total amount of commands saved, cause it happened that the commands I am searching for are just out of reach. Since this is low IO and storage these days is cheap, I don't see a point not to increase it hugely. I changed HISTSIZE and HISTFILESIZE to 1000 and 10000.

changed in <code class="EnlighterJSRAW" data-enlighter-language="null">~/.bashrc</code>

<pre>HISTFILESIZE=10000
HISTSIZE=1000</pre>

Why two variables ? In short HISTSIZE is the in memory storage per session, while HISTFILESIZE is the "long term" storage. ([source][1])

Another handy [bash variable][2] is HISTCONTROL; In the current version of GNU Bash (history is a part of GNU Bash), 3 options are available;

  * _ignorespace_ : when you enter a line starting with a space, its not saved in history
  * _ignoredups_ : when a command is repeated, only the first instance is saved
  * _erasedups_ : when a command is entered, it is checked with previous commands and those are removed. For example when running regular backups of MySQL with mysqldump only the latest mysqldump line is saved. (this makes it more of a "library")

The ignore[_space_|_dups_] also have an alias _ignoreboth._

I also added this to <code class="EnlighterJSRAW" data-enlighter-language="null">~/.bashrc</code>

<pre>HISTCONTROL=ignorespace:ignoredups:erasedups</pre>

When using history, an indication of when this command was issued could be useful. In case you wanne look what your drunken collegae did at three in the morning under root. This can be achieved with HISTTIMEFORMAT, again bash builtin variable, it internally calls a [strftime][3]() so you can setup how to visualize the date. I don't care for seconds or years (to specific, to unspecific) So I left those out, note that you best end with a space, as it gets concatenated with the command.

<pre>HISTTIMEFORMAT="%H:%M %m/%d "</pre>

The last GNU Bash variable is HISTIGNORE, this filters out commands. I removed some that I know by heart, although ignoredups and erasedups are probably more then enough to catch most redundant commands. Its separated by colon.

<pre>HISTIGNORE="ls":"ll":"pwd":"free *"</pre>

This would filter out ls, ll (alias of ls -l) and pwd. The command free with flags would also be fully ignored (free -m, among the most known ones)

The last setting to play with is [shopt][4] this is the shell option "manager". You wanne change _histappend _this will append the history to the history file, I don't exactly know why this is not on by default. It occurred that multiple commands where gone from history, while I was certain I entered them. This might be a issue with multiple sessions overwriting one another. This might be "solved" with this. You can check for this option :

<pre>shopt | grep histappend</pre>

its either on or off, enabling or disabling can be done from console with :

<pre># enable
shopt -s histappend

# disable
shopt -u histappend</pre>

Although I just appended it to  <code class="EnlighterJSRAW" data-enlighter-language="null">~/.bashrc</code>

That all being said, I found it a very useful find. However if you hit on the cases I described at the start of this article, removing a single line can be accomplished with :

<pre>history -d LINE_NUMBER</pre>

or if you wanne hide your tracks :

<pre>history -c</pre>

Will remove all history. (not recommended)

_note : I tested this with GNU bash, version 4.1.2(1) your mileage might be different!_

All together :

<pre>HISTFILESIZE=10000
HISTSIZE=1000
HISTCONTROL=ignorespace:ignoredups:erasedups
shopt -s histappend</pre>

Update : I removed HISTIGNORE, as it also ignores "ls *" if you use arrow-up key. So there is no way to find the dir you just typed. I commonly use :

<pre>ls /data/
ls /data/somedir
ls /data/somedir/with
ls /data/somedir/with/something/</pre>

Typelog : "ls /dat", tab, arrow up, som, tab arrow up, wit tab arrow up, som tab

some nice sources / examples of history :

  * [Keeping separate history logs][5] (tip!)
  * [The original finder of these bash vars ?][6]
  * [15 examples to work with bash][7]

 [1]: http://stackoverflow.com/questions/19454837/bash-histsize-vs-histfilesize
 [2]: https://www.gnu.org/software/bash/manual/html_node/Bash-Variables.html
 [3]: http://php.net/manual/en/function.strftime.php
 [4]: http://ss64.com/bash/shopt.html
 [5]: http://moonpup.blogspot.be/2007/11/keeping-separate-history-files-for.html
 [6]: http://blog.macromates.com/2008/working-with-history-in-bash/
 [7]: http://www.thegeekstuff.com/2008/08/15-examples-to-master-linux-command-line-history/