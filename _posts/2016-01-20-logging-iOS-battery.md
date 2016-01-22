---
published: true
layout: default
title:  " Logging iOS Battery to Thingspeak"
date:   2016-01-20 18:14:00
categories: article
---
# The Problem : a very short battery life
Lately, my iPhone 6 battery has not been lasting as long as it used to. This shouldn't be a surprise to me or anyone who uses smartphones running on lithium batteries. I've read several blogs and tried several troubleshooting steps such as disabling of background refresh, minimizing push notifications, making the screen dimmer, and disabling location, and using manual mail refresh. These didn't seem to help all that much, and at the end of the day, you end up with a phone that isn't much fun, and you'd  just as well turn the thing off if you're that desprate for battery life. (*Yes, I'm aware there's a settings that shows the top battery consumptions items, but in my case all the apps seem fairly low, and there's nothing out of the ordinary there*).

What surprised me was that it seemed as if the *rate* the battery was declining compared to previous had changed so dramatically over such a short period of time. I wasn't sure if it was my imagination, or it was fact so I decided to take matters into my own hands. I created a small [workflow](https://workflow.is/workflows/9ea2c71acbe84bffb40d95517f741a07) that would grab the iOS battery life, copy to clipboard and then send it to [Thingspeak](http://www.thingspeak.com) so I could graph my battery usage over time. Ideally I'd also be able to add a comment or log of sorts to track different things I tried to improve (or worsen) battery live. For example, I could log to a csv file with something like: 

	090312232015,56%,'enabled Find My Friends'

Then over time I'd be able to see larger dips in battery power decline on the pretty spline graphs it creates. The Thingspeak API would take the time stamp and battery percentage and graph it, and I'd also have a data log of what was done at various points in the day. So far, all I've been able to do is to log battery % and the time stamp is taken care of automatically by Thingspeak. I'm still working on how to log comments and correlate them later. 

# The Technical Solution
To log my battery usage to Thingspeak I needed to use a couple services and hook them together. First I used [workflow.is](http://workflow.is) to create a small [workflow](https://workflow.is/workflows/9ea2c71acbe84bffb40d95517f741a07) that gets battery % and logs it to the clipboard. 

![workflow Screencap](http://media.davidkanter.com/battery-thingspeak.jpg)

After this, I used pythonista to grab the clipboard value (the battery in our case) and send to thingspeak.


	# Pythonista Script to send iOS battery life in clipboard from workflow to thingspeak.
	import requests 
	import clipboard
	import webbrowser
	battery = clipboard.get()
	print ("battery level is: " + battery)
	x=requests.post('https://api.thingspeak.com/update?	key=SECRET_API_KEY_THINGSPEAK&field5='+battery)
	print(x)
	# next line optional
	url = 'https://thingspeak.com/channels/67532/private_show'
	webbrowser.open(url)

[Download gist here](https://gist.github.com/dmkanter/908200332c289209e46f)

I could do this entire script in workflow, but it seems to open safari every time I execute the URL call, and I'd rather do it from Pythonista for some reason.  The last line will open the URL of my dashboard in pythonista. In the future, I'd like to have it just make the web service call from the background without having to open a browser at all.

The only problem with the system is that I need to remember to execute it manually several times throughout the day. I wish I could just have it execute hourly, and perhaps ask me to enter a comment, much like Day One prompts me to make a periodic journal entry. 

To make it a bit easier, I made the workflow script accessible from the iOS Widget Screen
![img](http://media.davidkanter.com/widget-2015-12-24-13-31.jpg)
 
 I've been playing with other dashboards such as [freeboard.io](http://www.freeboard.io) and [dweet.io](http://www.dweet.io). Hopefully over the next few weeks, I'll start to have an idea of what's taking up my battery life. Here's a view of the past few weeks. I hope I figure it out soon!
 
![img](http://media.davidkanter.com/Screenshot-2015-12-24-13.35.13.png) 