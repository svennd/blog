---
title: Reset Supermicro IPMI Password From Windows
author: svennd

date: 2015-09-04T12:15:16+00:00
url: /reset-supermicro-ipmi-password-from-windows/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin
  - windows

---
I recently had an issue where we lost the password for IPMI to a brand new Supermicro server. The server was running Windows. Not wanting to mess around rebooting the box to a livecd I had to find a solution to reset the password. This could be very useful for those of you buying a used Supermicro server or if someone fat fingers a password or logs it incorrectly into your management system.

First you will need the IPMI Configuration utility. I got a copy for windows (32-bit and 64-bit) here:

Download [IPMICFG-Windows.exe][1]

Unzip your download and place either the 32-bit, 64-bit, or both directories somewhere accessible to you from a command prompt. I’m using 64-bit so I just copied that directory. Boot up the Administrator command prompt and ‘cd’ into the appropriate directory.

Once there, here are the commands we’re going to run:

<pre>C:\Users\Administrator\Desktop\64bit&gt;IPMICFG-Windows.exe -fd
Reset to the factory default completed.
C:\Users\Administrator\Desktop\64bit&gt;IPMICFG-Windows.exe -dhcp off
Successfully disable DHCP.
C:\Users\Administrator\Desktop\64bit&gt;IPMICFG-Windows.exe -m x.x.x.x
IP=x.x.x.x
C:\Users\Administrator\Desktop\64bit&gt;IPMICFG-Windows.exe -k 255.255.255.xxx
Subnet Mask=255.255.255.x
C:\Users\Administrator\Desktop\64bit&gt;IPMICFG-Windows.exe -g x.x.x.x
Gatway=x.x.x.x</pre>

Obviously you will need to specify the correct IP adress, subnet mask and gateway. The application will echo back the result to you. It’s important you turn DHCP off if you are going to use a static IP. Otherwise just leave DHCP on, and reset it to default with the first command and pass it the empty ‘m’ flag to see the current IP from DHCP.

Once your IPMI is online you should be able to login to it via the default username and password of ADMIN. Once logged in as the ‘ADMIN’ user, you will want to change your password, because ‘ADMIN’ is not a secure password. Do so from the configuration menu.

// update - 21/09/2021

When updating the password from console, if you get :

<pre>IPMI command failed: Request data field length limit exceeded
Set User Password command failed (user 2)
</pre>

You need to pick a stronger password.  
Password requirements:

  * Require password length: 8 to 19 characters
  * Password can not be reverse of the user name
  * Password must include characters from **at least 3** of the listed character classes
  * Allowed character classes: 
      * a - z
      * A - Z
      * 0 - 9
      * Special characters

&nbsp;

 [1]: ftp://ftp.supermicro.com/CDR-B9_1.00_for_Intel_B9_platform/IPMI/IPMICFG/Windows/IPMICFG-Windows_v1.10.zip