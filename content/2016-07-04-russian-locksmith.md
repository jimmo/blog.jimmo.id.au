Title: Russian locksmith
Date: 2016-07-04
Category: misc

An accidental project that I forgot to write about at the time…

A friend of mine at work came to me with a problem -- he has some special key fob thing to get into his apartment building, and no way to get a second copy for his partner. Or the strata wanted to charge some exorbitant fee or something. But the fob makes an electrical connection so surely it must be possible to clone it somehow.

![Dallas iButton]({static}/images/0*Ju4dqKBFY3LMW-Gq.jpg)

It’s called a [Dallas iButton](https://www.maximintegrated.com/en/products/digital/ibutton.html) and apparently they’re also very common in Russia. Also, my friend used to live in Moscow and he claims that every locksmith there can clone them. Not so in Sydney. The part itself is a [DS1990A](https://www.maximintegrated.com/en/products/digital/memory-products/DS1990A.html).

A bit of research showed that it uses the 1-wire protocol -- which is where you have ground and a combined power/signal wire. Yes, this technically means that there are two wires, but that’s not the point. Internally the device has enough capacitance to keep it’s microcontroller running while either side pulls the signal low. So the device is connected via a pull-up resistor to the supply voltage and all devices have open-drain configuration to pull the bus low. It supports single-master multiple-slave on the same bus. I remember hearing someone at work talk about it being used for a temperature sensor network for a project he was involved in. Unbelievably, they used to make 1-Wire buttons that ran a JVM.

But how to 1-wire? Fortunately, I’d just a few weeks earlier gotten a [BusPirate](http://dangerousprototypes.com/docs/Bus_Pirate) partly just as a useful tool that you never know when you’re going to need but as it also could function as a JTAG interface and an AVR ISP and a bunch of other things it seemed like a good purchase. I’ve since used it for both of those things (JTAG programming for CPLDs and flashing AVR chips).

![BusPirate]({static}/images/0*cPzkC1agoakOQ3eL.jpg)

The way it works is pretty straightforward -- the master queries the bus, and the device responds with its serial number. So now we knew what we had to clone. The BusPirate worked really well for this. They have a [how-to guide](http://dangerousprototypes.com/docs/IButton_serial_number) which we were able to follow with no problems.

Next step -- turns out it’s easy to buy the DS1990A part, but they’re factory-made with a unique serial number (normally I guess you program your security system with additional codes). But you can get (very unofficial) RW1990 parts that are programmable. So off to Aliexpress, wait a few weeks, and we had a pack of 5.

The datasheets for this part were a bit hard to find, and also in Russian. A few blog posts too, also in Russian. But fortunately, my friend speaks Russian! It uses a slightly different timing than 1-wire, which ruled out the BusPirate, but it was fairly straightforward to implement this on an AVR. What took a really long time was that I was using the wrong value for the pull-up resistor -- eventually with a 220ohm pull-up, it worked perfectly. Makes sense that it would need more current for programming -- putting the scope on it make the voltage sag obvious.

![1-wire programming with an Arduino]({static}/images/0*_UypL4pQAIMjBrny.jpg)
