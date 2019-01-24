---
layout:	post
title:	"Zigbee packet capture"
date:	2017-02-25
---

  I’ve been struggling with getting the lights to work reliably. In particular one situation which is causing a lot of problems is when you turn on room, the lights should all send an “announce” ZDO. I find that the coordinator, on average, sees about 75% of them.

What this means is that the software has a really hard time figuring out whether a given light is turned on or not. Also, I was keen to make the controller automatically restore a bulb to its previous configuration on power-on. Easy fix — ping all the devices periodically, but this led to further problems:

* If a device is offline, you have to wait for it to timeout. This slows down the scan time for the whole house.
* Sometimes you don’t get replies to the pings either.
* Pings add more traffic to the network.

My theory was that either:

* The network is handling contention badly — multiple devices send their “announce” ZDOs at the same time, they collide, and they don’t retry.
* The devices (bulbs) are unreliable — they sometimes forget to send their announce ZDOs on power on.
* The XBee coordinator is dropping messages on the floor.

At [linux.conf.au 2017](https://linux.conf.au/) I attended an excellent talk by Jon Oxer about [Network Protocol Analysis for IoT Devices](https://www.youtube.com/watch?v=3BNNY6_r3tQ). A key point — everything is easier when you can debug the network layer. So I went looking for an 802.15.4 sniffer. I followed a few paths to various dead ends, but came to the conclusion that the best bet was a [TI CC2531](http://www.ti.com/tool/CC2531emk) USB module. This is an 8051-based microcontroller with a 802.15.4 radio transceiver. Importantly, it comes supplied with firmware that allows you to use a [Windows-based TI tool for packet capture](http://www.ti.com/tool/packet-sniffer), but also allows you to develop custom firmware. In particular, it’s supported by [Contiki OS](http://www.contiki-os.org/) which includes a sample application called [“sensniff”](https://github.com/contiki-os/contiki/tree/master/examples/sensniff), that does packet capture, with a [Python program that can write it in pcap format](https://github.com/g-oikonomou/sensniff) for [Wireshark](https://www.wireshark.org/).

{% include figure.html url="/assets/img/1*6_-biUkBycv6D2iKhXwm-Q.jpeg" caption="TI CC2531" %}

Very exciting! The kit arrived, but despite having a USB interface, you can’t actually program it without a [dedicated programmer](http://www.ti.com/tool/CC-DEBUGGER). Argh! So, another Digikey order. In hindsight this was super obvious, but frustrated that I missed it.

In the meantime, I experimented with the built-in firmware. The TI capture tool works fine on Windows but is fairly limited in its protocol support. Also, took me a while to realize that when Digi’s X-CTU says that my coordinator is on channel “14” it really means 0x14.

While I waited for the Digikey order, I figured out how to build the Contiki firmware. On Arch Linux, mostly this involves setting up an 8051 toolchain (SDCC) and this was fairly straightforward using the [instructions on the Contiki Wiki](https://github.com/contiki-os/contiki/wiki/8051-Requirements). Note: pay careful attention to the line that says “Recent Tested SDCC revisions: 9092” — you definitely need to get that revision, see below.

Also I figured it would be good to be able to program the device from Linux too — after following [this forum post](https://e2e.ti.com/support/wireless_connectivity/low_power_rf_tools/f/155/p/144696/524846#524846) to [this GitHub repo](https://github.com/dashesy/cc-tool) I was able to get that up and running too.

{% include figure.html url="/assets/img/1*KCeo7QQRB5Rh5x4Vt0T3lQ.jpeg" caption="TI CC-Debugger." %}

But it didn’t work. Very frustrating, because I couldn’t even make the Contiki blink demo work. Turns out something is broken in SDCC later than SVN revision 9092. Reverting back to 9092 and I had working firmware! Let me know if you’d like a built image to flash.

You can run the [sensniff Python tool](https://github.com/g-oikonomou/sensniff) with

```
> $ python3 sensniff.py -b 406800 -d /dev/ttyACM0 -D DEBUG
```

which will create a pipe full of pcap events which you can open in Wireshark with

```
> $ wireshark-gtk -ni /tmp/sensniff -k
```

Next to tell Wireshark how to decrypt the HA-profile data. I found [this video](https://community.nxp.com/thread/331972) which was extremely helpful, but the summary is:

* In Preferences > Protocols > Zigbee, set “Security Level” to AES-128 / 32-bit.
* Edit the keys and add “5A:69:67:42:65:65:41:6C:6C:69:61:6E:63:65:30:39” with byte order “normal” and label something like “Zigbee Trust Center Link Key”. (Fun fact: that key is the hex of “ZigBeeAlliance09”).
* Now grab a frame and open up “Zigbee Network Layer Data / Zigbee Security Header” and grab the transport key, and add it alongside the first key. *This key is private to your network and is given to devices during joining.*

{% include figure.html url="/assets/img/1*OxiKK5gPMCF5KMlTjyzCrA.png" caption="Finding the transport key in the security header of a captured packet." %}

{% include figure.html url="/assets/img/1*sMhGRmFwv0VZeh5Iw_hJYg.png" caption="Configuring the Zigbee keys in Wireshark preferences." %}

Awesome! Now we get fully decrypted packets in Wireshark. So, what does it look like when I turn on a room?

{% include figure.html url="/assets/img/1*PYOaHRrHLnCKOczBRov-vg.png" caption="Interesting!" %}

So I’m fairly sure this debunks the “devices don’t do retries” theory, and it suggests that collisions probably aren’t a thing. Looking at you, XBee…

Looking forward to running this while using my home automation controller and seeing if I can iron out the remaining issues. I have one theory about the XBee which might be that packets are arriving so quickly that they’re filling the UART TX buffer, which is why they’re being dropped. This suggests three possible things to look into:

* Run the UART at a higher speed
* Switch to SPI (at an even higher speed) instead of serial.
* Is the host not pulling data from serial fast enough (serial->FTDI->USB->TTY->pyserial). **Update: late-2018: Yes!**

Will investigate further. Perhaps it’s time to move the coordinator to the TI CC2531… more on that next post.
