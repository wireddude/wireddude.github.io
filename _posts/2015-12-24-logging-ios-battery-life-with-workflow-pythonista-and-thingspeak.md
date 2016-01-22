---
id: 340
title: Logging iOS battery life with Workflow, Pythonista, and Thingspeak
date: 2015-12-24T13:39:53+00:00
author: wireddude
layout: post
guid: http://kangtai.me/?p=340
permalink: /logging-ios-battery-life-with-workflow-pythonista-and-thingspeak/
categories:
  - blog
  - ios
tags:
  - battery
  - ios
  - metrics
  - python
  - pythonista
  - thingspeak
---
# The Problem

Lately, my iPhone 6 battery has not been lasting as long as it used to. This shouldn&#8217;t be a surprise to me or anyone who uses smartphones running on lithium batteries. I&#8217;ve read several blogs and tried several troubleshooting steps such as disabling of background refresh, minimizing push notifications, making the screen dimmer, and disabling location, and using manual mail refresh. These didn&#8217;t seem to help all that much, and at the end of the day, you end up with a phone that isn&#8217;t much fun, and you&#8217;d just as well turn the thing off if you&#8217;re that desprate for battery life. (_Yes, I&#8217;m aware there&#8217;s a settings that shows the top battery consumptions items, but in my case all the apps seem fairly low, and there&#8217;s nothing out of the ordinary there_).

What surprised me was that it seemed as if the _rate_ the battery was declining compared to previous had changed so dramatically over such a short period of time. I wasn&#8217;t sure if it was my imagination, or it was fact so I decided to take matters into my own hands. I created a small [workflow](https://workflow.is/workflows/9ea2c71acbe84bffb40d95517f741a07) that would grab the iOS battery life, copy to clipboard and then send it to [Thingspeak](http://www.thingspeak.com) so I could graph my battery usage over time. Ideally I&#8217;d also be able to add a comment or log of sorts to track different things I tried to improve (or worsen) battery live. For example, I could log to a csv file with something like:

    090312232015,56%,'enabled Find My Friends'
    

Then over time I&#8217;d be able to see larger dips in battery power decline on the pretty spline graphs it creates. The Thingspeak API would take the time stamp and battery percentage and graph it, and I&#8217;d also have a data log of what was done at various points in the day. So far, all I&#8217;ve been able to do is to log battery % and the time stamp is taken care of automatically by Thingspeak. I&#8217;m still working on how to log comments and correlate them later.

# The Technical Solution

To log my battery usage to Thingspeak I needed to use a couple services and hook them together. First I used [workflow.is](http://workflow.is) to create a small [workflow](https://workflow.is/workflows/9ea2c71acbe84bffb40d95517f741a07) that gets battery % and logs it to the clipboard.

<img src="http://i0.wp.com/media.davidkanter.com/battery-thingspeak.jpg?w=604" alt="workflow Screencap" data-recalc-dims="1" />

After this, I used pythonista to grab the clipboard value (the battery in our case) and send to thingspeak.

    # Pythonista Script to send iOS battery life in clipboard from workflow to thingspeak.
    import requests 
    import clipboard
    import webbrowser
    battery = clipboard.get()
    print ("battery level is: " + battery)
    x=requests.post('https://api.thingspeak.com/update? key=SECRET_API_KEY_THINGSPEAK&field5='+battery)
    print(x)
    # next line optional
    url = 'https://thingspeak.com/channels/67532/private_show'
    webbrowser.open(url)
    

[Download gist here](https://gist.github.com/dmkanter/908200332c289209e46f)

I could do this entire script in workflow, but it seems to open safari every time I execute the URL call, and I&#8217;d rather do it from Pythonista for some reason. The last line will open the URL of my dashboard in pythonista. In the future, I&#8217;d like to have it just make the web service call from the background without having to open a browser at all.

The only problem with the system is that I need to remember to execute it manually several times throughout the day. I wish I could just have it execute hourly, and perhaps ask me to enter a comment, much like Day One prompts me to make a periodic journal entry.

To make it a bit easier, I made the workflow script accessible from the iOS Widget Screen
  
<img src="http://i0.wp.com/media.davidkanter.com/widget-2015-12-24-13-31.jpg?w=604" alt="img" data-recalc-dims="1" />

I&#8217;ve been playing with other dashboards such as [freeboard.io](http://www.freeboard.io) and [dweet.io](http://www.dweet.io). Hopefully over the next few weeks, I&#8217;ll start to have an idea of what&#8217;s taking up my battery life. Here&#8217;s a view of the past few weeks. I hope I figure it out soon!

<img src="http://i2.wp.com/media.davidkanter.com/Screenshot-2015-12-24-13.35.13.png?w=604" alt="img" data-recalc-dims="1" />