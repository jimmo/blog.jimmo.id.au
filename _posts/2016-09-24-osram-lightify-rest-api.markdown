---
layout:	post
title:	"Osram Lightify REST API"
date:	2016-09-24
---

  I’ve made lots of progress on the home automation project. Lights, switches, groups, actions, colours, oh my!

The main part of this project is the Osram Lightify bulbs that we put in when we renovated the house. Basically very similar to the Phillips Hue but cheaper. However, when I was using them with the supplied hub and app, they were super buggy.

I also have a set of [SmartenIt ZBWS3B switch panels](http://smartenit.com/product/zbws3b/). They don’t talk to the Osram stuff, and my plan to use SmartThings didn’t work out because of the network latency (to “the cloud”) and how frustrating it was to set up any sort of automation in SmartThings.

So I set off on this ridiculous path to learn how ZigBee works and build a replacement hub based on a XBee S2C module. Finally, it kind-of works. More posts about that shortly though — today just a quick update on “other approaches that don’t quite do what I want”.

I’ve been running into some issues with reliability (an important feature of home lighting!). I’m pretty sure this is a reception issue, but I figured that doing a software update on the bulbs wouldn’t hurt. Unfortunately, this meant resetting all of them and re-pairing them with the Lightify hub, then leaving it for 5 hours while it updated all the bulbs. I did notice though that there have been huge improvements to the Lightify app. It seems a lot more reliable and the groups support has been improved. Additionally in the release notes I saw something about a REST API, which was super exciting.

It’s pretty easy to use — <https://us.lightify-api.org/> or <https://eu.lightify-api.org/> depending on where you bought your bulbs. Using curl, I was able to get a list of devices and groups and send on/off/colour messages. Very cool. The only problem is the latency. It was at least 5 seconds between sending the request and the bulbs changing. Unfortunately I’m stuck using the EU endpoint because I bought my bulbs from Germany, so I bet this latency could be reduced if I could use the US one — I don’t know how chatty the protocol is though.

Even if the latency were better though, it’s still no good if you want to integrate switches or other sensors or devices (although I see that Osram are selling switches and motion sensors now). Especially if you want to do more interesting actions.

But either way — I don’t want the cloud involved in turning on a light. I was hoping the hub itself would have the REST API on it. The hub does listen on port 4000 and I found <https://github.com/mikma/python-lightify> which works nicely — all of the group and bulb functionality that I tested worked fine. I’d initially written off using the Osram hub because using it with the app was so buggy, but this does look pretty useful. Still doesn’t help with supporting the switches though.

I think I’ll keep forging ahead with my own ZigBee controller, and finish implementing the group/action/event/scheduling stuff. Future firmware updates for the bulbs are a total pain, but functionality-wise I’m getting there. My goal is to put the XBee together with a PyBoard into a little self-contained unit.

The reliability issues manifest as devices not responding but all signs point to this either being a range/reception problem (it’s worse for the further-away bulbs, probably solved by adding a router node and/or better positioning of the coordinator) or a firmware bug (I’ve seen the same issue today with the Osram hub).

  