---
title: 'WPScan & Wordfence'
author: svennd

date: 2015-07-19T06:46:57+00:00
url: /wpscan_wordfence/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
Suddenly the entire world is talking about hacking "again", there is a [awesome new serie](http://www.imdb.com/title/tt4158110/" target="_blank) (_mr. robot_) and I recently stand corrected on hacker "gear" in _[sense 8][1]_ also a great serie, that is on netflix.  The names where pretty fancy tho, [rage master](https://www.schneier.com/blog/archives/2014/03/ragemaster_nsa.html" target="_blank), [water witch](https://www.spiegel.de/static/happ/netzwelt/2014/na/v1/pub/img/Mobilfunk/S3224_WATERWITCH.jpg" target="_blank), [switch hooks](https://en.wikipedia.org/wiki/Phreaking" target="_blank), [CANDYGRAM](https://www.schneier.com/blog/archives/2014/02/candygram_nsa_e.html" target="_blank) are not something we regular hear about, but they do exist ! Also the story about the hacking team, is pretty crazy. ([good summery about hacking team](http://www.engadget.com/2015/07/09/how-spyware-peddler-hacking-team-was-publicly-dismantled/" target="_blank)) Anyway my brain was looking in to security again.

**WPScan**

Is my WordPress installation save ? Did I use some dangerously outdated plugin ? Is my template safe ? Is something giving away data ? Security is not easy and very time consuming. So I was happy to find that, there is a great tool for these kind of tests, called [wpscan][2] (_WPScan is a black box WordPress vulnerability scanner_). Running it is as easy as -after installation- :

<pre class="EnlighterJSRAW" data-enlighter-language="null" data-enlighter-linenumbers="false">ruby wpscan.rb --url http://my_blog.ext</pre>

What is even more fun, you can start a brute force on accounts of the blog! I was surprised to learn that WordPress doesn't per default include a limitation at the amount of login attempts can be made and as such you have to rely on plugins, as otherwise, this leaves the door open to brute force attacks.

**Wordfence**

<span style="line-height: 1.5;">I was checking what kind of security measurements are in WordPress, allot of plugins pop-up when searching for it, however adding more code to a project generally decreases the security and doesn't make it faster generally. The exception seems to be </span><a style="line-height: 1.5;" href="http://www.wordfence.com" target="_blank">Wordfence</a><span style="line-height: 1.5;">, stating that they make your website up to 50 times faster, and more secure since they limit the amount of logins over time. While that does seem to be to good to be true, </span><a style="line-height: 1.5;" href="https://ma.ttias.be/benchmarking-the-performance-of-wordfence-a-wordpress-plugin/" target="_blank">the data seems</a> <span style="line-height: 1.5;">to be there, so <em>Wordfence</em> it is!</span>

&nbsp;

 [1]: http://www.imdb.com/title/tt2431438/
 [2]: http://wpscan.org/