---
title: HipChat with IRC
date: 2013-11-09 19:00:00 -05:00
hidden: true
---

*Note: Just use Slack and an IRC client*

At Oak we communicate mostly via HipChat, I also lurk on IRC though and managing both is less than ideal. Enter [BitlBee](http://www.bitlbee.org/main.php/news.r.html), it tunnels IM protocols to IRC, and luckily HipChat supports Jabber ([sort of](http://help.hipchat.com/knowledgebase/articles/64377-xmpp-jabber-support-details)).

If not already, install BitlBee with `brew install bitlbee` or by using [their downloads](http://www.bitlbee.org/main.php/download.html). Start the server with `bitlbee -F`, and setup your IRC client by adding the BitlBee server on `localhost:6667`. Join the `&bitlbee` channel and register with the command `register SomePassword`. When connecting to the server in the future you’ll need to identify using `/msg &bitlbee identify SomePassword`.

Grab your HipChat Jabber information from the [XMPP/Jabber Account page](https://www.hipchat.com/account/xmpp) in your account settings. It should look something like this:

![XMPP/Jabber Account page](/assets/Screen Shot 2013-10-17 at 11.57.18 PM.png)

The following command adds the HipChat account to bitlbee. Be sure to replace “JabberID” with your actual jabber id.

```json
account add jabber JabberID@chat.hipchat.com
```

The following two commands are optional, the first makes nick names display correctly, the second disables chat history replay when joining a room.

```json
account hipchat set nick_source full_name
account hipchat set resource bot
```

Turn on the HipChat account with `account hipchat on` and join the newly created `&hipchat` channel.

The next two commands you’ll need to enter for each room you join. Make sure to use the correct room name and jabber name. HipChat also requires your nick to match the one on their server, so set it appropriatly. If you enter it wrong they will complain and correct you.

```json
chat add hipchat JabberName@conf.hipchat.com #RoomName
channel #RoomName set nick 'Jane Smith'
```

If nothing went awry, join the new room: `/join #RoomName`.