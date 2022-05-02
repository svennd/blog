---
title: Post message to Rocket.chat from bash
author: svennd

date: 2017-03-15T12:55:44+00:00
url: /post-message-to-rocket-chat-from-bash/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
We recently moved over from _Slack_ to a self-hosted communication app : _[Rocket.chat][1] _. One of the things I really enjoyed on slack, was the fact that there is a huge amount of integrations. Even from bash one can write to _slack. _This is particularly nice for reports of cron jobs, previously I relied on e-mails, but those tend to get ignored/spammed/... not read. So I let cron jobs also slack me. On Rocket.chat there are also incoming hooks, but I could not find a script that made it dummy prove to use like the one from _[Sul Aga][2] _so I took his code and adapted it to work with _Rocket.chat. _Here is how you can do the same.

<!--more-->

#### Create a new incoming webhook

under administration -> integrations : add new incoming webhook

  * enabled: true
  * channel : #channel
  * script-enabled : true
  * script : <pre>class Script {
  process_incoming_request({ request }) {
    console.log(request.content);

    return {
      content:{
        text: request.content.text
       }
    };

    return {
       error: {
         success: false,
         message: 'Error example'
       }
     };
  }
}</pre>

The only thing you need now is the _Webhook URL._

#### Sending messages : The script

Based on [this script][2].

<pre>#!/usr/bin/env bash

function usage {
    programName=$0
    echo "description: use this program to post messages to Rocket.chat channel"
    echo "usage: $programName [-b \"message body\"] [-u \"rocket.chat url\"]"
    echo "      -b    The message body"
    echo "      -u    The rocket.chat hook url to post to"
    exit 1
}

while getopts ":b:u:h" opt; do
  case ${opt} in
    u) rocketUrl="$OPTARG"
    ;;
    b) msgBody="$OPTARG"
    ;;
    h) usage
    ;;
    \?) echo "Invalid option -$OPTARG" &gt;&2
    ;;
  esac
done

if [[ ! "${rocketUrl}" || ! "${msgBody}" ]]; then
    echo "all arguments are required"
    usage
fi

read -d '' payLoad &lt;&lt; EOF
{"text": "${msgBody}"}
EOF

echo $payLoad

statusCode=$(curl \
        --write-out %{http_code} \
        --silent \
        --output /dev/null \
        -X POST \
        -H 'Content-type: application/json' \
        --data "${payLoad}" ${rocketUrl})

echo ${statusCode}</pre>

Now to use it is simple; Take webhook url, and start posting messages.

<pre>./postToRocket.sh -b "everything is fine today" -u ROCKET_CHAT_URL</pre>

And that's it folks ! Happy spamming Rocket.chat 🙂

 [1]: https://rocket.chat/
 [2]: http://www.sulhome.com/blog/12/post-messages-to-slack-from-bash