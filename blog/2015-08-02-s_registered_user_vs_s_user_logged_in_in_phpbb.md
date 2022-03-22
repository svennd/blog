---
title: Difference between phpBB 3.1 S_REGISTERED_USER and S_USER_LOGGED_IN
author: svennd

date: 2015-08-02T12:49:56+00:00
url: /s_registered_user_vs_s_user_logged_in_in_phpbb/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - code
  - Linux

---
While working on a phpBB forum, I found this weird template var, (at first look) namely : <code class="EnlighterJSRAW" data-enlighter-language="null">&lt;!-- IF S_REGISTERED_USER --&gt;</code> while there was just a bit higher <code class="EnlighterJSRAW" data-enlighter-language="null">&lt;!-- IF S_USER_LOGGED_IN --&gt;</code> while it does seem a bit weird its in fact a simple check for bots, S\_REGISTERED\_USER will only be validated when a user is not a bot. As such the following example does make sense.

<pre>&lt;!-- IF S_USER_LOGGED_IN --&gt;
You are logged in. #user #bot #3th_option
    &lt;!-- IF S_REGISTERED_USER --&gt;
    Since you are registered as a user, you are most likely a human, or a spambot who got through.
    &lt;!-- ELSE --&gt;
    Since you are logged, and are not a registered user, I believe you must be a known BOT;
        &lt;!-- IF S_IS_BOT --&gt;
         Now i'm 100% sure your a bot.
        &lt;!-- ENDIF --&gt;
    &lt;!-- ENDIF --&gt;
&lt;!-- ELSE --&gt;
You are definitely not logged in, so you are probably a user or a bot I don't know.
&lt;!-- ENDIF --&gt;</pre>

Useful summary : <del>phpBBModders : If Statements.</del> (down... for good ?)