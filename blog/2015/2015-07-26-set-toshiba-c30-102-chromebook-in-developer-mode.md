---
title: Set Toshiba C30-102 Chromebook in developer mode
author: svennd

date: 2015-07-26T18:09:43+00:00
url: /set-toshiba-c30-102-chromebook-in-developer-mode/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
tags:
  - c30-102
  - chromeos
  - toshiba

---
Getting into developer mode (dev. mode) is rather easy, but is needed to install tools like [crouton][1], and well, pretty much everything else you would like to install offline, that is not available in the cloud.

For this model you need to shutdown your _Chromebook_ then press ESC + refresh + power button. (during boot) Make sure no USB device or SD stick is in, otherwise ChromeOS will look for an image there. This phase is called Recovery mode, now that you are there, press Ctrl-D, ChromeOS will now boot in dev-mode!

 [1]: https://github.com/dnschneid/crouton