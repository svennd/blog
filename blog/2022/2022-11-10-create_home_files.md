---
author: "Svenn D'Hert"
title: "Truenas on automake homedir + file creation"
description: ""
date: 2022-11-09
thumbnail: /img/2022/11/single-earth.jpg
url: /truenas_auto_file_home/
---
To create an info file (for example) while using the `pam_mkhomedir` module, you can achieve this by placing a file in the `/etc/skel` directory. Alternatively, if the skel directory is set to a different location in the PAM configuration files, you can drop the file there.


info : 
- https://www.truenas.com/docs/core/coretutorials/sharing/smb/homeshare/


Image by [singleearth](https://unsplash.com/@singleearth)
