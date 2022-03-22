---
title: 'wkhtmltopdf: error while loading shared libraries: libXrender.so.1'
author: svennd

date: 2016-04-15T14:53:16+00:00
url: /wkhtmltopdf-error-while-loading-shared-libraries-libxrender-so-1/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
I'm playing (trying to) with [wkhtmltopdf ][1]I'm running in some errors though ... here are the packages that seem to fix it :

<pre>wkhtmltopdf: error while loading shared libraries: libXrender.so.1</pre>

libXrender

<pre>yum install libXrender</pre>

next :

<pre>./wkhtmltopdf: error while loading shared libraries: libfontconfig.so.1: cannot open shared object file: No such file or directory</pre>

fontconfig and urw-fonts

<pre>yum install fontconfig urw-fonts</pre>

next ;

<pre>error while loading shared libraries: libXext.so.6:</pre>

libXext

<pre>yum install libXext</pre>

And finally :

<pre>./wkhtmltopdf http://google.com test.pdf
Loading pages (1/6)
Counting pages (2/6)
Resolving links (4/6)
Loading headers and footers (5/6)
Printing pages (6/6)
Done</pre>

Itttt isssssa working !

 [1]: http://wkhtmltopdf.org