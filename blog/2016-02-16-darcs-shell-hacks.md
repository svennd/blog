---
title: Darcs shell hacks
author: svennd

date: 2016-02-16T11:36:24+00:00
url: /darcs-shell-hacks/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - code
  - Linux

---
<img loading="lazy" class="alignnone wp-image-985" src="/img//2015/07/25451392-1.png" width="242" height="79" srcset="/img/2015/07/25451392-1.png 242w, /img/2015/07/25451392-1-3x1.png 3w" sizes="(max-width: 242px) 100vw, 242px" />[darcs][1] is a version control system, such as [svn][2], [git][3], [mercurial][4], ... while its losing the popularity match against pretty much all other vcs's it's still used where I work. I'm not a huge fan, but that's beside the point. For reporting matter (eg. bosses) its nice to show a fancy graph, that productivity is growing over the years. Hence I find myself in need of getting historical data from darcs, darcs unlike the other vcs's don't have allot of nice fancy tools for pulling graphs. So here I'd like to share some shell "hacks" to get them.

<!--more-->

Getting commits for each year :

<pre>for VARIABLE in {2000..2016}; do 
darcs changes --xml-output | grep "&lt;patch"  | grep "date='$VARIABLE" | grep eter | wc -l; 
done</pre>

Getting commits for a single person for each year, in this case "tom", _note that names of authors might change : Tom, tom, lastnameT, ..._

<pre>for VARIABLE in {2000..2016}; do 
darcs changes --xml-output | grep "&lt;patch"  | grep "date='$VARIABLE" | grep tom | wc -l; 
done</pre>

Getting all Lines of code over patches. (#patch 1 : 100, #patch 2 : 105, ...) ([source][5])  
_note : cat /tmp/darcslog will contain a list from last patch to first patch with the lines of code._

<pre>darcs trackdown 'find . -type f -print | xargs wc -l | tail -1 | tee -a /tmp/darcsloc; false'</pre>

Show all authors :

<pre>darcs show authors</pre>

Show repo info (including #of patches)

<pre>darcs show repo</pre>

&nbsp;

Know any other hacks/scripts for darcs, feel free to share!

 [1]: http://darcs.net
 [2]: https://subversion.apache.org
 [3]: https://git-scm.com
 [4]: https://www.mercurial-scm.org
 [5]: http://chneukirchen.org/blog/archive/2004/11/loc-statistics-for-darcs.html