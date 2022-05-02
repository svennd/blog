---
author: "Svenn D'Hert"
title: "apt-get key deprecation"
description: "apt key deprecation"
date: 2022-04-28
thumbnail: /img/2022/05/remi-jacquaint-0uoz5nC.jpg
url: /apt-key-deprecation/
---

Got this small annoying "error" while running `apt-get update`. Seems that apt-key is deprecated;

```
W: https://linux.teamviewer.com/deb/dists/stable/InRelease: Key is stored in legacy trusted.gpg keyring (/etc/apt/trusted.gpg), see the DEPRECATION section in apt-key(8) for details.

```

This is the quick and dirty way :
```
mv  /etc/apt/trusted.gpg /etc/apt/trusted.gpg.d/
```

A better solution is described here, on [askubuntu](https://askubuntu.com/questions/1398344/apt-key-deprecation-warning-when-updating-system)

$key = key you wish to transfer to new systemn check with `apt-key list`.
$keyname = name you can choose

```
# export key
apt-key export $key

# import back into file format
gpg --dearmour -o /usr/share/keyrings/$keyname.gpg

# change repo file
deb [arch=amd64 signed-by=/usr/share/keyrings/$keyname.gpg] https://linux.teamviewer.com/deb/dists/ stable main

# delete key
apt-key del $key
```



img by [jack_1](https://unsplash.com/@jack_1)
