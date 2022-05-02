---
title: 'List all commands in bash/Linux : compgen'
author: svennd

date: 2016-02-20T14:25:31+00:00
url: /list-all-commands-in-bashlinux-compgen/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
When people say you learn something new every day, its mostly a saying, meaning yeez, I feel stupid for not knowing that. well this is a new one for me today. Its a build-in GNU/bash command called _compgen_.  Using compgen you can find all commands and aliases available to you in the current session.<!--more-->

> Generate possible completion matches for word according to the options, which may be any option accepted by the complete builtin with the exception of -p and -r, and write the matches to the standard output. When using the -F or -C options, the various shell variables set by the programmable completion facilities, while available, will not have useful  
> values.
> 
> The matches will be generated in the same way as if the programmable completion code had generated them directly from a completion specification with the same flags. If word is specified, only those completions matching word will be displayed. The return value is true unless an invalid option is supplied, or no matches were generated.
> 
> **bash manual**

**Commands**

List all the commands :

<pre>compgen -c</pre>

List all commands and aliases :

<pre class="EnlighterJSRAW">compgen -ac</pre>

This would be the way you search in it :

<pre># compgen -ac | grep bzip
bzip2recover
bzip2
</pre>

+1 Linux knowledge!