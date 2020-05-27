---
description: Sync Pia with the calendar on your smartphone
---

# Calendar

## Setup

1. We'll have to assume that you already have a calendar server with CalDAV, like NextCloud or ownCloud or Google calendar. We plan to include a simple CalDAV/CardDAV server in the docker setup, so that you can sync your smartphone directly with Pia over Wifi.
2.  Configure the your server account
   1. Edit `config.json`section `calendar`
   2. Enter `serverURL` ,`username`and `password`. For example:

      ```text
        "calendar": {
          "serverURL": "https://nextcloud.example.com/remote.php/dav",
          "username": "fred",
          "password": "wilma"
        },

      ```
3. Restart Pia

## Voice commands

Try saying:

* What are my upcoming appointments?
* Tell me my appointments for today
* Which my appointments do I have on Saturday?
* What is my next appointment?
* Add an appointment with Wilma on Saturday 2 PM \(not implemented yet\)



