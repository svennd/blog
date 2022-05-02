---
author: "Svenn D'Hert"
title: "Moving to Hugo"
description: "Moving over 8 years of posts!"
tags: ["hugo", "WordPress"]
date: 2022-03-12
thumbnail: /img/2022/03/hugo_fp.jpg
url: /Moving-to-Hugo/
---
After more then 8 years of WordPress, I moved my blog over to [hugo](https://gohugo.io). I still like & love WordPress, but it was becoming too bloated to my liking. I only use a very small subset of its features and every plugin becomes freemium model. Even the basic security plugins!

On my 5 &euro; / month VPS resources are limited and the PHP & MySQL stack take quit some resources. Using Hugo, I only need resources to build the website, after that everything is served statically. This can be cached way more easily by CloudFlare, speeding up the website and significantly lowering the amount of wasted resources.

I'm just playing around with Hugo and I like it. I wanted to track the "regex" changes I made to the export of WordPress. Using the [WordPress-to-hugo-exporter](https://github.com/SchumacherFM/wordpress-to-hugo-exporter).

I'm using the nice, minimalistic theme, [blist](https://github.com/apvarun/blist-hugo-theme)

## Changes :

My theme doesn't use "type: post", so I removed the line.
```
type: post
to
(nothing)
```

### Images and such
Move them to static directory. I prefer the nice structure WordPress uses. (year/month/asset.jpg)
```
/wp-content/uploads/
to
/img/
```
I could edit the theme, but renaming is easier.
```
featured_image:
to
thumbnail:
```

Some images where not converted from html to markdown so I manually did them.
```
<img .*?src="(.*?)" alt="(.*?)".*? />
to
![$2]($1)

and

https://www.svennd.be/img/
to
/img/
```

Some of the posts never got a featured_image, so I created a "default" for them. Using the [beautiful unsplash library](https://unsplash.com/about)

```
# create a thumbnail default for all
url:(.*?)$
to
url:$1\nthumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg

# but now we created a double for some, so remove the first again.
thumbnail:(.|\r?\n)*?thumbnail:(.*?)$
to
thumbnail:$2
```

### html encoding
Not strictly necessary, but some other themes do fall over pure html tags
```
&#8220; => "
&#8221; => "
&#8217; => '
&#8216; => '
&#8211; => -
&#215; => x
&#8230; => ...
```

Also some headers where not converted, so I did them manually. (of course for h1->h6)
```
<h4>(.*?)</h4>
to
#### $1
```

trivia :
```
<pre.*?>
to
<pre>

<em>(.*?)</em>
to
_$1_

remove :
<p>
</p>
```

links :
```
<a href="(.*?)">(.*?)</a>
to
[$2]($1)
```
