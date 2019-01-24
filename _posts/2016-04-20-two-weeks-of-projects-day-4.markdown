---
layout:	post
title:	"Two weeks of projects: Day 4"
date:	2016-04-20
---

  Today was mostly spent on the CPU project again. With the lexer and parser done yesterday I pretty much finished the assembler. I’m still really slow in Go but it’s getting better.

Some background on the CPU design:

* It has 6 8-bit registers, A-F
* A is the accumulator
* B is the second argument (for binary ops like “add”)
* Memory is always accessed at the 16-bit concatenation of C and D
* There’s an 8-bit input port, and an 8-bit output port
* Flags Z (zero), C (carry), N (negative), V (twos-complement overflow)

The instruction set is really basic. You can do one of four things:

* Store a 4-bit immediate value in the low or high nibble of A-D
* Copy from an 8-bit source to and 8-bit destination. The source can be registers A-F, the input port, or the RAM byte at C:D. Similarly the destination can be registers A-F, the output port, or RAM at C:D.
* Branch, either unconditional or based on flags (jump if zero, jump greater or equal, etc) to the ROM address C:D.
* An ALU operation (add, xor, etc).

To keep the design simple, all instructions encode to a single byte. That’s why there’s no 8-bit load immediate. This has a fairly straightforward encoding. There might be some benefit later to moving the bits around (e.g. making it so that “rr” aligns with the low bits of “ddd”) to simplify the decoder.

```
0rrn xxxx -- load immediate (n=1 high, n=0 low) (rr=A,B,C,D)
10dd dsss -- copy sss to ddd
110? tttt -- branch (tttt=type)
111? ffff -- ALU (ffff=function)The assembler uses “mov” for both load immediate and copy
```

```
mov %al, 0xc ; write 0xc to low-nibble of A
mov %a, %b ; copy B to A
mov %mem, %b ; copy B to *(C:D)
mov %out, %c ; copy C to output port
```

Writing 4-bit loads gets tedious, so I added a few shortcuts that expand out during assembly — the beginnings of a macro assembler.

```
mov %a, 0xab
; mov %al, 0xb
; mov %ah, 0xamov %a:b, 0xabcd
; mov %al, 0xb
; mov %ah, 0xa
; mov %bl, 0xd
; mov %bh, 0xcmov %c:d, $somelabel
; mov %c:d, address-of-somelabelThis is why I made C:D the memory address, not E:F (you can’t write immediate to E or F) and you quite often want to do something likemov %c:d, 0xface
mov %a, %mem
mov %c:d, $end
```

jzE and F are mostly just useful as temporary stash locations. I think I’ll eventually want to add a macro for copy 16-bit and things like inc/add 16-bit and multiply.I started writing some basic programs and it’s really quite painful! Maybe we should add a compiler (GCC or LLVM backend!) to the project list. Here’s a test program that counts to 0xff and fills the first 265 bytes of memory with that sequence.<https://gist.github.com/jimmo/b4aa8e6c4fadd2c6dcfe14959d9a93e3>Anyway, that was fun. Tomorrow I’m going to write a disassembler and make a start on the emulator.
