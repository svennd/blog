---
author: "Svenn D'Hert"
title: "ACL on Truenas"
description: ""
date: 2022-03-28
thumbnail: /img/2022/03/ubuntu_sge.jpg
---



https://www.reddit.com/r/BSD/comments/2bvw60/eli5_setfacl_permissions/

https://www.slideshare.net/andy_leonard/when-acls-attack

```
setfacl -m group@:rwxpdDaARWcs:fd:allow support
setfacl -m group@:dD::deny support
```

syntax : 
```
setfacl -m level:permissions:inhertance:allow|deny dir/file
```
level : 
- owner@ : owner of the dir/file
- group@ : group that owns the dir/file
- everyone@
- owner
- group
- everyone

permissions : 
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

