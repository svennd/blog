---
title: Session validation failed bareos webui
author: svennd

date: 2017-03-10T16:24:48+00:00
url: /session-validation-bareos/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Just playing around with bareos-webui and suddenly I'm greeted with a 500 error on the website. The director was running nicely and /var/log/messages did not show anything. Since this is a web server error, luckily there is something logged in the <code class="EnlighterJSRAW" data-enlighter-language="null">/var/log/httpd/error.log</code> :

<pre>[Fri Mar 10 16:38:08.009365 2017] [:error] [pid 11266] [client hidden-ip:64292] PHP Fatal error:  Uncaught exception 'Zend\\Session\\Exception\\RuntimeException' with message 'Session validation failed' in /usr/share/bareos-webui/vendor/zendframework/zend-session/src/SessionManager.php:130\nStack trace:\n#0 /usr/share/bareos-webui/module/Application/Module.php(76): Zend\\Session\\SessionManager-&gt;start()\n#1 /usr/share/bareos-webui/module/Application/Module.php(43): Application\\Module-&gt;initSession(Object(Zend\\Mvc\\MvcEvent))\n#2 [internal function]: Application\\Module-&gt;onBootstrap(Object(Zend\\Mvc\\MvcEvent))\n#3 /usr/share/bareos-webui/vendor/zendframework/zend-eventmanager/src/EventManager.php(444): call_user_func(Array, Object(Zend\\Mvc\\MvcEvent))\n#4 /usr/share/bareos-webui/vendor/zendframework/zend-eventmanager/src/EventManager.php(205): Zend\\EventManager\\EventManager-&gt;triggerListeners('bootstrap', Object(Zend\\Mvc\\MvcEvent), Array)\n#5 /usr/share/bareos-webui/vendor/zendframework/zend-mvc/src/Application.php(157): Zend\\EventManager\\EventManager-&gt;trigger('bootstrap', Object(Zend\\Mvc\\MvcEvent))\n#6 /usr/share/bareos-web in /usr/share/bareos-webui/vendor/zendframework/zend-session/src/SessionManager.php on line 130</pre>

It seems its a known error for bareos-webui, the workaround is deleting the cookie related to bareos-webui. Thanks to this [google post][1].

 [1]: https://groups.google.com/g/bareos-users/c/f_WQLwQ-hu0
