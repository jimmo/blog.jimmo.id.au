Title: CPLD CPU: Part 1, Overview
Date: 2019-01-26 22:30:00 +1100
Category: Projects

This is part 1 of a series about building a CPU out of small components implemented in CPLD. Here's the rough outline of the series, I will update it as I write each post. The CPU is actually built and works well, so I'm looking forward to writing the later posts and showing some demos!
 - [Part 1, Overview (this post)](/2019/01/26/cpld-cpu-part-1.html)
 - [Part 2, Simulation](/2019/01/26/cpld-cpu-part-2.html)
 - Part 3, First attempt
 - Part 4, Writing programs
 - Part 5, Simplify
 - Part 6, Second attempt
 - Part 7, Memory
 - Part 8, GPIO
 - Part 9, Implementing in VHDL
 - Part 10, Designing a PCB
 - Part 11, Building it
 - Part 12, Programming it
 - ... likely more to come

Way back in 2016 I mentioned an interest in building a functional CPU out of small building blocks. This project has been largely inspired by three amazing projects:

 - [The Megaprocessor](http://megaprocessor.com/) -- A CPU built from giant panels of through-hole transistors and LEDs. This is truly one of my favourite things on the internet.
 - [Monster 6502](https://monster6502.com/) -- A MOS 6502 built from SMD transistors (and lots of LEDs).
 - [Fourbit](http://jaromir.xf.cz/fourbit/fourbit.html) -- A 4-bit CPU built entirely from 74xx ICs.

I also teach an introduction to "how CPUs work" at the [National Computer Science School -- Summer School](https://ncss.edu.au), a 10-day residential summer school for Australian high-school students. The aim is to demystify the magic between their code (in MicroPython) and things they can breadboard (transistors). It's a lot to cover and would definitely be easier with some hands-on demos!

The projects above inspired me to want to build something that would help make some of these concepts a bit more tangible. I definitely didn't want to build it out of transistors, so I initially started to look at extending the Fourbit design linked above. Unfortunately the ALU chips (74181 and 74182) proved hard to source, and co-incidentally around the same time a friend donated his electronics collection which contained a CPLD breakout board, so I looked into replicating those chips with CPLDs instead. At this point I realised that I could just do the whole thing using CPLDs, which would avoid a lot of unneeded complexity and allow a lot more flexibility.

As I've already mentioned on this blog, the CPLD I'm using is the [Xilinx XC9572XL](https://www.xilinx.com/support/documentation/data_sheets/ds057.pdf) and I have one of [these breakout boards from Dangerous Prototypes](http://dangerousprototypes.com/docs/XC9500XL_CPLD_breakout_board). Compared to, for example, the Xilinx Spartan 6 FPGA these things are *tiny*. 72 macrocells doesn't sound like much, but it's more than enough to implement a register or an ALU. And with the 44-pin package, and 34 user I/O pins, that should be enough to make everything 8-bit and leave enough room for a wide memory bus (e.g. a component can have 3 8-bit busses and still have 12 other I/O lines free).

Yes, this whole thing could just be implemented in an FPGA, even a really tiny one. It would be much simpler too! However the idea is to make it possible to see what's going on -- every signal should have an LED on it. That said, there's also no real reason why the individual components have to be CPLDs. For a while I considered using individual microcontrollers instead. As long as they run at a high enough clock rate compared to the CPU's clock, it would work in the same way. But getting enough GPIO was tricky, and I liked the idea of writing them in VHDL, which would give the ability to see the implementation visualised as the logic diagram produced by the Xilinx tools.

Having barely written any VHDL since university over 15 years ago, I wrote a simple bit of VHDL to flash the LED on the breakout board and decided that was sufficient experience to go ahead with the project.

Some other notes:
 - I want this CPU to be simple enough that it's understandable without having to know a lot of additional CPU design concepts. i.e. all instructions are the same length and execute in the same number of cycles, memory operates at a high enough speed that it can be treated like a register, etc.
 - It should demonstrate some really fundamental concepts like memory mapped IO.
 - It should actually be able to execute a "real" program. At a minimum, compute prime numbers, or interact with some external sensor over the GPIO pins. Maybe even run a simple game.
 - Unlike the Megaprocessor, I do not want it to conflict with my living room. "[This could be where the conflict in scale between the Megaprocessor and my living room starts to escalate.](http://megaprocessor.com/progress.html)", but like the Megaprocessor, more LEDs is more better.

[Up next: let's build it...in a simulator...that we're going to build from scratch.](/2019/01/26/cpld-cpu-part-2.html)
