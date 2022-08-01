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

or 
```
add_file 

w 

Permission to add a new file to a directory. 

add_subdirectory 

p 

On a directory, permission to create a subdirectory. 

append_data 

p 

Placeholder. Not currently implemented. 

delete 

d 

Permission to delete a file. 

delete_child 

D 

Permission to delete a file or directory within a directory. 

execute 

x 

Permission to execute a file or search the contents of a directory. 

list_directory 

r 

Permission to list the contents of a directory. 

read_acl 

c 

Permission to read the ACL (ls).

read_attributes 

a 

Permission to read basic attributes (non-ACLs) of a file. Think of basic attributes as the stat level attributes. Allowing this access mask bit means the entity can execute ls(1) and stat(2).

read_data 

r 

Permission to read the contents of the file. 

read_xattr 

R 

Permission to read the extended attributes of a file or perform a lookup in the file's extended attributes directory. 

synchronize 

s 

Placeholder. Not currently implemented. 

write_xattr 

W 

Permission to create extended attributes or write to the extended attributes directory. 

Granting this permission to a user means that the user can create an extended attribute directory for a file. The attribute file's permissions control the user's access to the attribute. 

write_data 

w 

Permission to modify or replace the contents of a file. 

write_attributes 

A 

Permission to change the times associated with a file or directory to an arbitrary value. 

write_acl 

C 

Permission to write the ACL or the ability to modify the ACL by using the chmod command.

write_owner 

o 

Permission to change the file's owner or group. Or, the ability to execute the chown or chgrp commands on the file.

Permission to take ownership of a file or permission to change the group ownership of the file to a group of which the user is a member. If you want to change the file or group ownership to an arbitrary user or group, then the PRIV_FILE_CHOWN privilege is required.
```

```
Inheritance Flag 

Compact Inheritance Flag 

Description 

file_inherit

f

Only inherit the ACL from the parent directory to the directory's files. 

dir_inherit

d

Only inherit the ACL from the parent directory to the directory's subdirectories. 

inherit_only

i

Inherit the ACL from the parent directory but applies only to newly created files or subdirectories and not the directory itself. This flag requires the file_inherit flag, the dir_inherit flag, or both, to indicate what to inherit.

no_propagate

n

Only inherit the ACL from the parent directory to the first-level contents of the directory, not the second-level or subsequent contents. This flag requires the file_inherit flag, the dir_inherit flag, or both, to indicate what to inherit.

-

N/A 

No permission granted. 

Currently, the following flags are only applicable to a CIFS client or server. 

successful_access

S

Indicates whether an alarm or audit record should be initiated upon a successful access. This flag is used with audit or alarm ACE types. 

failed_access

F

Indicates whether an alarm or audit record should be initiated when an access fails. This flag is used with audit or alarm ACE types. 

inherited

I

Indicates that an ACE was inherited. 
```
examples : 

```
# allow svennd full control on budget (no inherit), :: resolves into :----:
setfacl -m u:svennd:rwxpDdaARWcCos::allow Budget
```

sources :        
- https://www.reddit.com/r/BSD/comments/2bvw60/eli5_setfacl_permissions/
- https://www.slideshare.net/andy_leonard/when-acls-attack
- https://docs.oracle.com/cd/E19120-01/open.solaris/817-2271/ftyxi/index.html


Image by [rojekilian](https://unsplash.com/@rojekilian)
