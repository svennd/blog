---
author: "Svenn D'Hert"
title: "firewalld"
description: "Firewalld!"
date: 2023-02-14
thumbnail: /img/2022/05/clay-banks.jpg
url: /firewalld-intro/
---


Get all zones
```
firewall-cmd --get-zones
```

query zones (default:public)
```
firewall-cmd --list-all
firewall-cmd --list-all --zone=public
firewall-cmd --list-all --zone=internal
```

check the applications
```
firewall-cmd --zone=public --list-services
```

add application to zone
```
# add it
firewall-cmd --zone=external --add-service=ftp

# reload : gone again
firewall-cmd --reload

# add it perm
 firewall-cmd --zone=external --add-service=ftp --permanent
```

remove application from zone 
```
firewall-cmd --permanent --zone=external --remove-service=ftp
```

add specific port
```
firewall-cmd --permanent --zone=external --add-port=60001/udp
```

# create custom zones
```
firewall-cmd --permanent --new-zone=my_personal_zone
firewall-cmd --permanent --zone=my_personal_zone --add-interface=eth0
firewall-cmd --remove-interface=enp0s8 --zone=my_personal_zone
```

