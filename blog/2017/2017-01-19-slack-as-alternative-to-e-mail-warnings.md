---
title: Slack as alternative to e-mail warnings.
author: svennd

date: 2017-01-19T15:05:55+00:00
url: /slack-as-alternative-to-e-mail-warnings/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
When monitor systems are made, there are only a few options, e-mail, telephone and some constricting alternative nobody ever heard off. Well goodbye to those. E-mails get lost, or in spam, telephones are fine, only Y2K skype called, and they want there platform back. So here goes my next attempt : slack 🙂 [LibreNMS][1] already polls all my devices over the network, and can report straight to slack. (and also e-mail and 50 other options) Time to also update my emailing cron jobs to slack's.

I used [slack for bash][2], written by [Sul Aga][3]. Using it is easy :

**Install**

<pre>cd /opt
git clone https://github.com/sulhome/bash-slack
ln -s /opt/bash-slack/postToSlack.sh /usr/local/bin/postToSlack</pre>

**Use **

<pre>postToSlack.sh -t "title of your message" -b "content of the message" -c "serverroom_channel" -u "https://hooks.slack.com/services/..." &gt; /dev/null
</pre>

The URL can be generated from an [incoming webhook][4]. Messages look like this :

![webhook using bash](/img/2017/01/slack_webhook_bot.png) 

Note that the output will tell you the status code (200: OK) I ignore it, since there is no other way then to ... mail on failure ?

 [1]: https://github.com/librenms/librenms
 [2]: https://github.com/sulhome/bash-slack
 [3]: http://www.sulhome.com/blog/12/post-messages-to-slack-from-bash
 [4]: https://api.slack.com/incoming-webhooks