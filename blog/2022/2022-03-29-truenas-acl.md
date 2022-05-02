---
author: "Svenn D'Hert"
title: "ACL on TrueNAS"
description: "Setup ACL on TrueNas"
date: 2022-03-29
thumbnail: /img/2022/04/sarah-kilian-ACL-truenas.jpg
url: /ACL-on-TrueNAS/
---

ACL is not something I'm really familiar with, so these are just raw notes on how I deal with it in TrueNAS. Some more context might follow :laughing:

check permissions with
```
getfacl
```


set them (chmod/chown) :  (the order seems to be important)
```
setfacl -m group@:rwxpdDaARWcs:fd:allow support
setfacl -m group@:dD::deny support
```

syntax :
```
setfacl -m level:permissions:inhertance:allow|deny dir/file
```

levels :
- owner@ : owner of the dir/file
- group@ : group that owns the dir/file
- everyone@ : others ?
- owner : a specific user other than the owner of the file/dir
- group : a specific group other than the owner of the file/dir
- everyone : ?

Permission letters :
```
         r      read_data
         w      write_data
         x      execute
         p      append_data
         d      delete_child
         D      delete
         a      read_attributes
         A      write_attributes
         R      read_xattr
         W      write_xattr
         c      read_acl
         C      write_acl
         o      write_owner
         S      synchronize
```

sources :        
- https://www.reddit.com/r/BSD/comments/2bvw60/eli5_setfacl_permissions/
- https://www.slideshare.net/andy_leonard/when-acls-attack


Image by [rojekilian](https://unsplash.com/@rojekilian)
