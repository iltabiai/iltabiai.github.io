---
layout: post
title:  "Yet Another Slack Tips and Tricks post"
date:   2016-06-08 13:32:00
comments: true
categories: [slack, organize]
tags: slack, organize
---

### Channels

You can **mute** any channel to stop getting notifications by typing `/mute` in the channel. Use it for channels that you only check from time to time. You will still be notified if someone directly mentions you. 

You can **leave** a channel by typing `/leave` in the channel.

You can invite anyone to a channel simply by typing their username in that channel. You must then click on `Invite`.

Use the **Do Not Disturb** mode if you do not want to be disturbed at all for some time. Simply type `/dnd 1 hour` and you will not be bothered for the next hour.

To send a notification to all users in a channel, use `@channel` in your message. To send a notification to a specific user use `@username`. Use `@here` to send a notification to the presently active users in a channel (anyone using DND or absent will not be notified). Use `@everyone` to send a notification to every member of the team.

### Formatting

Press the **up** arrow to edit your last message, then press **Enter**.

* **Bold**: `*text*`

* _Italics_: `_text_`

* ~Strikethrough~: `~text~` 

* `Verbatim`: insert the text in-between ` marks



### Set reminders

**Get started with**: `/remind help`

**Typical usage**: `/remind me to Call Tamir next Thursday at 3PM`

You will receive a reminder at Thursday 3PM that you can either mark as completed, or ask Slackbot to remind you again some other time. You can setup reminders for anyone in the same Slack team using their username instead of `me`. You will be noitifed by SlackBot once that person marks the reminder as complete.

Check you existing reminders by typing: `/remind list`

Other examples:

```
/remind me to drink water at 3pm every day
/remind @jessica about the interview in 3 hours
```



### Tiny database

It is possible to setup [custom SlackBot answers](https://get.slack.help/hc/en-us/articles/202026038). These answers are triggered by specific keywords or sentences.
Its most common usage is for the Wifi password. If a message contains both words **wifi** and **password** or **key**, then SlackBot should provide the WiFi key.

It can be used as _electronic post-its_ to remember things...


### Integrations or apps

A large number of apps or integrations are available for Slack. An integration either adds functionality to Slack or plugs it to other services.
The most common integration to expand functionality is [Giphy](http://giphy.com/posts/slack-adds-giphy-to-every-chatroom-wut); once installed, it is possible to type `@giphy [text]` in any channel to display a gif related to the `[text]` written. 

Slack can also be connected to Github and Trello for example. In that case, any update in a Github repo will appear in a certain channel. Other users can immediately react and discuss the update, wether it is a Github update, a Trello one, Google Drive, etc...



{% include twitter_plug.html %}

