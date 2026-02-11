Title: Two weeks of projects
Date: 2016-04-17
Category: Projects

After over seven years there, Friday was my last day at Google. I’m going to be starting at my new job in two weeks time, so that gives me 16 days (over a million seconds!) of project time. I’ve been getting my workbench set up over the past few months and I’m super excited.

![Workbench]({static}/images/0*mLxl_lCJNJwaj77B.jpg)

There’s a few things on the list, but here are the main items:

* Get the smart lighting working. When we renovated the house, we put Osram Lightify bulbs in. The bulbs are excellent, the hub/controller and the Android software are not. I’ve got a working prototype of a [XBee module](http://www.digi.com/support/productdetail?pid=4838) talking to a single bulb to control on/off, brightness, colour, colour temp. I want to turn that into a useful automation controller for the whole house (all 37 bulbs) and some the Zigbee switch panels, motion sensors, door sensors, etc.
* Build a functional “educational CPU”. I’ve wanted to do this since university when we made 4-bit CPUs in a simulator. I’d like to build a roughly half-panel board where each component (register, ALU, instruction decode) of the CPU is implemented on a CPLD. Then route all the signals in a way and silkscreen it such that it’s (relatively) clear what’s going on. And of course, put hundreds of LEDs on all the bus and signal lines for tracing/debugging. I’ve got a basic 4-bit design, but would like to make it 8-bit. I’ve used the [Xilinx XC9572XL](http://www.xilinx.com/support/documentation/data_sheets/ds054.pdf) before and it seems like a good fit.
* Build a “boat computer”. Not sure exactly what that means at this stage, but I have these amazing eInk displays which would be perfect for a screen on a race boat. A starter project would be something along the lines of a [ProStart](http://www.velocitek.com/prostart) or a [SpeedPuck](http://www.velocitek.com/speedpuck/) for a dinghy -- GPS, compass, data logger. The bigger project is to build all the components that a race yacht would use -- mast displays, cabin displays, integrate with wind, water speed, GPS, plotter, polar calculations, next-tack angles, etc. I’ve got code that can drive the eInk modules, so looking to take that a bit further.
* Finish moving into the house. There’s still a giant pile of books under the stairs, the upstairs room still has a bunch of stuff not in cupboards, there’s plenty to do in the garden, etc.
* Learn some guitar. This has been on my list forever. Two weeks will not be enough time but it’s worth putting some effort in.
* Get fitter. Go jogging or bike riding every weekday.
* Blogging. I’m going to try and post at least once a day to keep track of progress (and as a way to keep myself motivated to finish stuff every day).

I’ll write posts on these as I work on them.

There are other projects too. A friend gave me a [LPCXpresso](https://www.lpcware.com/lpcxpresso) dev board, so that seems like a great way to get started with some ARM development (for the projects where an AVR is a bit too limited). I also have these little ultrasonic transducers that I’d like to see if I can measure wind speed/direction with a ring of them. Not sure if that’ll even work but it’ll be interesting to see.

And on the programming side, I’d like to learn something about [TensorFlow](https://www.tensorflow.org/), and it’s finally time to do some Go or Rust (or both!).

And we’re going to New Zealand for a few days in the middle!
