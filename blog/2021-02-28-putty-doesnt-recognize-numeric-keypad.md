---
title: Putty doesn’t recognize numeric keypad
author: svennd

date: 2021-02-28T11:21:33+00:00
url: /putty-doesnt-recognize-numeric-keypad/
thumbnail: /img/2020/05/pc_2.jpg
categories:
  - Linux
  - windows

---
I love [Putty ][1], its my go-to ssh client emulator on Windows, recently I got this problem that it would not accept my keyboard's numeric keypad.  I have a pretty cheap remote keyboard, but it works nicely so why is Putty pestering me ?

Turns out Putty has a special Application Keypad mode, in which the keyboard can send special sequences to a server; I just want my numeric part of the keyboard for its numeric capabilities; there is a setting which helps here, and I only discovered this after a few weeks ... don't be like me google the solution;

&nbsp;

  * right click on the terminal and go to settings
  * Terminal -> Features -> Disable application keypad mode

Totally l33t !

![putty_disable_application_keypad_mode](/img/2021/02/svennd_putty.png)

 [1]: https://www.putty.org/