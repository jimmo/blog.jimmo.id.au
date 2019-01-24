---
layout:	post
title:	"Two weeks of projects: Day 5"
date:	2016-04-21
---

  Today felt productive but really that’s just because the things I was working on were easier. Lesson learnt!

Now I have a fully functional assembler and disassembler, and a mostly functional emulator. It can run that program from yesterday.

I spent a long time talking with some friends last night about this design — and some interesting things came up in the discussions:

* The goal of this project isn’t to build a CPU that is nice to program for. I’d love to add some hardware stack support (stack pointer, push, pop, etc) but that will dramatically increase complexity of the hardware implementation. There are some features that are on the borderline, for example a swap register-register instruction, but I can’t see a way to do that without adding more clock ticks per instruction (currently 4, adding swap would make it 5 or 6). It’s very tempting to keep suggesting more features, especially when you’re looking at it from the software side, or if you’re used to the hardware design of real CPUs.
* It’d be really nice to do a future “advanced” version of this project where you incrementally add some of these features, starting with variable-length instructions.* One feature that would be easy to add without complexity is making it so the ALU can write to registers other than A. There’s a free bit on the ALU encoding (111? ffff) so that could be used to specify A or B as output. Perhaps it should be A and C instead?
* Another one was interrupts. I’m going to consider adding an external interrupt pin which will result in a branch to a fixed address. Without a way to save register state or the current PC, this could be really hard to actually use effectively.
* We spent a lot of time thinking about different ways to design the instruction set. One change would be to get rid of register copies, and allow the ALU to set destination registers. So a mov becomes and add-zero-with-destination. Also we talked about things like adding a zero register. And of course an obligatory tangent into register vs stack machines.
* I probably don’t need both add and add-with-carry (and correspondingly sub and sub-with-carry) if I add a “clear carry”. So that frees up an ALU op. I also probably need to add a rotate-through-carry.
* It’s going to be moderately annoying that to update a pin on the output port, you have to know what the previous value was (i.e. there’s no way to read the current value of the output port). So you need to use a register and/or memory to keep this state. An obvious solution to this is to use memory-mapped IO instead. The benefit is that it frees up a register, the cost is that it makes the memory controller a tiny bit more complicated. I’m not sure that it’s worth it though — I quite like the simplicity of thinking of the I/O ports as registers.

I think I should work on something else tomorrow. Probably the lights.
