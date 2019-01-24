---
layout:	post
title:	"Two weeks of projects: Day 13"
date:	2016-04-29
---

  Time to talk about the home automation. I feel like this week has quickly turned into “Jim reads interesting stuff and writes about old projects while not actually making progress on those projects” but I’m OK with that. I lost a big chunk of today to other things that needed to be done, so I’m going to use the remaining time to write about home automation and Zigbee.

The background for the project is that we were doing major renovations to our house, and because every ceiling got replaced, we decided to put downlights (GU10 fittings) into every room. I thought it would be fun to do some sort of home automation, so I investigated options. They fall into two main categories:

* Regular bulbs with “smart” wiring:
* C-Bus. Commonly used in office buildings and occasionally in homes. The idea is that you run all the lights back to a central set of relays and the light switches themselves are just low voltage signalling for the central system. It’s very expensive and painful to set up and configure. Nope nope nope.
* Smart dimmers. Rather than a central dimmer/relay system, you have individual devices on the active line on each lighting circuit. Either wireless (often ZWave or commonly a proprietry tablet-based system) or some sort of low voltage signalling. One of the best examples I’ve seen is the [Fibaro Dimmer 2](http://www.fibaro.com/uk/the-fibaro-system/rgbw-controller).
* “Smart” bulbs with regular wiring:
* WiFi bulbs (like LIFX). These are actually very cool. The phone software is excellent, they have an easy API and the . The only problem is that they’re very expensive. At the time they sold a GU10 downlight fitting bulb, but I don’t see it on their website any more.
* ZWave bulbs (I haven’t actually seen many of these).
* ZigBee bulbs. The most popular example is the [Philips Hue](http://www2.meethue.com/en-au/). Other manufacturers like GE, Cree, Osram have also started making Zigbee bulbs.

The advantage of the first approach is that you get to use regular (cheap) bulbs and that you get a fully integrated solution. But cost argument is actually hard to justify because the rest of the system costs so much. The main disadvantage (and this is a big one!) is that if the system fails, then you’re stuck in the dark.

The idea with the Fibaro dimmers is that you can retrofit them to existing lighting by hiding them in the wall cavity. You don’t even need to do three-wire cabling because they can power themselves on just the active (althought you might need a bypass installed on the bulb).

I think if I were building a house today (even without the plan to do smart wiring immediately), I’d do C-Bus style wiring — i.e. run a cable from every bulb directly to a single location in the house, and similarly from every switch. Initially you can just wire them as the equivalent lighting circuits, but it gives you the easy opportunity to add something like Fibaro dimmers incrementally without having to open up wall cavities or get into the roof.

The second approach (smart bulbs) has the nice advantage that when all else fails you can continue to use your regular light switches (and the bulbs default to “on” after power cycle. On the other hand, the disadvantage of course is that your regular light switches will turn off the bulbs which means that you can’t control them when they’re off. So the most common use case is to then control them with your phone but only for the purpose of colour-changing or dimming.

I don’t know who actually wants to control their lights from their phone, so another requirement was to have some sort of switch panels that could control the bulbs.

ZWave has a major limiting factor which is that it uses the 900 MHz ISM band, and rather than using the part that overlaps across countries, it uses different frequencies in different countries. In particular, Australia uses a different frequency to the rest of the world. So this means that there’s a very limited supply of ZWave gear. Also the ZWave gear is expensive and I was also keen on the idea of regular wiring, so that pretty much ruled out both ZWave and the entire “smart wiring” category.

The WiFi bulbs looked great but I ended up ruling them out because it was very hard to find bulbs that cost less than $80–100 bulb. Also, there were very few GU10 bulbs available. Also I don’t want RGB downlights — just white/dimmable. Also, [this post by Matthew Garrett](https://mjg59.dreamwidth.org/40397.html) is a must read about the crappy end of the WiFi-bulb spectrum. Come for the bit about the GPL violations, stay for the bit about circumventing your network security.

So that just left Zigbee. The way this works is that you buy a starter kit from the manufacturer that includes a hub which acts as the WiFi/Ethernet to Zigbee bridge and a few bulbs. Zigbee itself is a 2.4GHz mesh protocol (802.15.4) and most manufacturers’ bulbs work as routers so whole-house coverage isn’t a big problem.

The obvious choice is Philips Hue, and probably if I lived in the US that would have been what I would have chosen.

* They have GU10 downlights (the Hue Lux).
* The software is excellent (both the phone app and the API).
* They have a whole range of other types of bulbs for other places in the house (RGB LED strips, orbs, RGB bulbs, garden lights).
* They have these amazing wireless switch panels (the Hue Tap) that don’t require batteries (what sourcery is this!?!) (edit: [this!](https://www.enocean.com/en/enocean_modules_2.4ghz/ptm-215ze/)).
* They integrate with a bunch of other things (including bulbs from other manufacturers).
* The hub connects over wired Ethernet, not WiFi.

But:

* The GU10 lights aren’t available in Australia.
* They’re really expensive.
* (At the time) the tap switches weren’t available.
* They did that horrible thing where they [deliberately broke support for other bulbs](http://www.cnet.com/au/news/philips-hue-reverses-course-will-continue-to-work-with-third-party-bulbs/).

Something else interesting about the Hue bulbs. They actually use the “Zigbee Light Link” (ZLL) Profile but the bulbs also support the “Home Automation” (HA) Profile. More on this in a later post about Zigbee.

I went looking for other options. One thing that kept popping up was [SmartThings](https://www.smartthings.com/). The basic idea is that it’s just the hub, but it can act as both a ZigBee and ZWave coordinator, and has compatibility for just about every single device out there (and a way of building your own device support with their development tools). Originally this was a kickstarter that sold a very successful v1 product, and has since been acquired by Samsung and now has a v2 model. Incredibly, the v2 product has removed a lot of very popular features — more on that later. One of the really cool features is the development environment — the device has an “app engine” where you can write your own custom workflows (“Smart Apps”) and device handlers and there’s a marketplace of sorts for people to share them. SmartThings also make a range of sensors (motion, proximity, window/door opening, etc).

This looked really cool — but it was remarkably difficult to learn about its capabilities and limitations without actually playing with one. One thing I was able to figure out was that there was a [Zigbee switch panel](http://smartenit.com/product/zbws3b/) that it would work with, which sounded good — I figured I could place them around the house, next to the “real” light switches.

After ruling out the Philips Hue bulbs, there wasn’t a lot of other options that were GU10 and 240V, so I went with the Osram Lightify range. I ordered a GU10 downlight and an RGBW A11 bulb and a hub to play with. Very quickly it became apparent that the bulbs are excellent but the hub (and phone software) is *awful*.

* To firmware update the bulbs, it can only do one bulb at a time. And it’s flakey as anything. So updating an entire house (37 bulbs) is a several-hour adventure.
* The software has no useful concept of “rooms”. This is unbelievable. So if I have four downlights in a room, I have to turn on each bulb individually. I can group the bulbs, but not turn them on as a group. There are ways to set up scenes and modes, but they don’t work.
* It’s just flat-out buggy. After sending a few commands to a bulb, it just stops responding. I don’t know if it’s the Android software or the hub, but it’s basically unusable. I later showed that it’s not the bulbs at fault.
* There’s no way to integrate with other devices.
* The hub only connects over WiFi, so your lighting is at the mercy of your WiFi network.

I ordered the SmartThings (with the plan to remove the ZWave antenna because it’s US-only) and one of the switch panels. The good news is that this let me prove that the Osram bulbs weren’t the cause of the unreliablity. I was able to set up the SmartThings to control the bulbs with the switch panels, and this was a good enough demo to convince myself to go ahead with Osram bulbs for the entire house. I managed to find them on amazon.de for €20 ($30 AUD) each.

Unfortunately it wasn’t until I tried to set up the whole house that I realized the limitations of the SmartThings.

* It *also* has no concept of rooms.
* Seriously! This is one of the v1 features that was removed in v2.
* I could work around this by setting up actions that had multiple target devices. This started to get very unwiedly though.
* Only the “blessed-by-Samsung” apps can actually run on the device. Worse still, it’s only the apps that make it into the firmware image.
* This dashed my hopes of writing custom workflow, but worse, the only decent lighting management app has zero chance of ever making into into the firmware.
* Turning on a room involves watching each bulb turn on one by one. Great way to visualize the AUUS RTT.
* The Android software is buggy as anything. I hear the iOS software is much better. Setting up a custom workflow is an exercise in frustration. To give it the benefit of the doubt, I assume this has improved in the past couple of months.
* Writing custom device handlers is very difficult. The documentation is simulateously unbelievable thorough but also confusing and weirdly out of date.
* The device detection for zigbee bulbs is confused because there’s multiple device handlers defined that support generic bulbs. The one it chooses is the wrong one. If you’re using SmartThings — make sure you’re using [this one](https://github.com/SmartThingsCommunity/SmartThingsPublic/tree/master/devicetypes/smartthings/zigbee-rgbw-bulb.src) (or the white-color-temperature equivalent).

I’d have a hard time recommending SmartThings to anyone. I’m simulatenously completely blown away by the incredible range of features and the whole development enviroment has to be seen to be believed — there’s a web based IDE thing with github integration and a simulator. As a programmer myself, I’m super impressed at the software engineering that’s gone into this thing. But it just feels like they never actually tried to use it to do actual home automation. The stock lighting controller app is primitive. (Edit: [Wired article about security flaws](https://www.wired.com/2016/05/flaws-samsungs-smart-home-let-hackers-unlock-doors-set-off-fire-alarms/)).

At this point it’s probably worth listing the features I had in mind. Home automation is one of those things where most people roll their eyes because the usefulness is margiral. I must admit I’m mostly doing it for fun, but here’s my list:

* Turn the lights on when we get home (either detecting our phone on the WiFi network or with a Zigbee keyfob).
* Motion sensors (e.g. turn the stair lights on at night time).
* Smart dimming. If I turn the light on at 3am, automatically make it 10%.
* Color temperature shifting. Cold in the morning, warm at night.
* Buttons that control multiple circits. I’d love a “turn off everything” button by the front door.
* Make up for the poor placement of the real light switches. At the moment there’s no way to turn on any downstairs lights from the bottom of the stairs.
* Have the bulbs in lamps also joined to the “room” lights. i.e. turning on the “lounge” should do the downlights and the two RGBW lamps.

These are just basic things. A friend of mine has a much more comprehensive list of scenarios. He’s going with the Fibaro ZWave kit.

I think I could probably make all of the above scenarios work, but in the end I gave up on the SmartThings. However, by then I had an entire house full of Zigbee bulbs, so the logical next step… build my own hub. You can buy Zigbee modules (even the one used in the SmartThings is on DigiKey) and I’ve heard so much about these XBee things — how hard could it be…?

Hard, as it turns out. More on that in another post.
