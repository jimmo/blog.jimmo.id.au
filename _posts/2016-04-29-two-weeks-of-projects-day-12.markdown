---
layout:	post
title:	"Two weeks of projects: Day 12"
date:	2016-04-29
---

  Still really distracted by that amazing list of blogs from yesterday. Two (related) things in particular really caught my attention though and I spent a lot of time reading today:

* <http://davidad.github.io/blog/2014/02/18/kernel-from-scratch/>
* <https://www.gitbook.com/book/0xax/linux-insides>

Turns out, last time I had “N weeks of projects” was school summer holidays, and my favourite project was “let’s write an operating system” — because that’s a totally reasonable goal for a high school kid who doesn’t really know C or assembly, much less how a computer boots (or well, anything about Unix). However (and I didn’t know it at the time) this was one of the highest value things I have ever spent my time on in terms of furthering my career and my skills. I spent hours pouring over the Intel reference manuals (and later discovered the AMD ones were even better). I learnt so much, and the things that didn’t make sense at the time sort of simmered in the background and helped me understand things later.

One of the things I really struggled with at the time was understandable reference material. The Intel/AMD docs were great, but they don’t cover a lot of really important details about the rest of the system (outside of the CPU). One of the key mistakes I made was obsessing about irrelevant details about making sure I was doing things the “right way” that would work on all possible hardware, rather than just getting it working and moving onto the next thing. (I guess I really thought that I was going to take over the world with my amazing operating system that had a database backed filesystem and a HTML renderer as its GUI — visionary stuff!). I got as far as a boot menu and some basic 32-bit protected mode graphics stuff.

Also, it was ~2000–2001 and I was doing most of this on BeOS, so there was no easy way to emulate this. VMWare had come out about a year or so earlier but wasn’t really in my price range (and didn’t run on BeOS). Bochs was usable but it seemed to be a lot stricter which made development harder. My development process involved a build script with a final step that dd’ed out to my hard disk, followed by a reboot. One really awesome feature of BeOS was how fast it could startup and shutdown. When the boot menu was broken I could boot from recovery CD, and when the OS was broken I could just reboot.

The first thing I built was an MBR boot menu — basically just select one of the four primary partitions to boot. The boot sector is 512 bytes, but you lose 64 bytes to the partition table and 2 bytes to the signature, so that leaves 446 bytes to make a GUI. And just the partition labels and various messages take up another 70. This was where I first learnt the “xor ax, ax” is two bytes, whereas “mov ax, 0” is four.

{% figure [caption:""] %}
![](/assets/img/0*scK_CAr0ah-hxydy.png)
{% endfigure %}

The interesting thing about the boot menu is that you don’t really have enough space to get into protected mode, so you do the whole thing in 16-bit real mode. I think a real boot menu would have code to load all the sectors from the first track. The boot menu’s job is to load the first sector from the selected partition and jump to that.

The next project was the actual OS. I think OS is the wrong word but I never came up with a better term. And of course, see earlier description of delusions. Most of the time was spent figuring out how to get to 32-bit protected mode, then some basic interrupt handling and video mode changing. I made a bitmap font and some basic text functions and blitting.

{% figure [caption:""] %}
![](/assets/img/0*AtYbfKMeZoHOO1Wa.png)
{% endfigure %}

What’s this got to do with project week? Reading the two articles linked at the top of this post was like seeing the worked solution guide to the problems I was working on almost 15 years ago. So cool. I decided today to see if I could get my old thing to boot. At some point in the early 2000s I started using VMWare on Windows so I’d written the buid tools (e.g. the font generator) using Win32. I re-wrote them today in Python and it booted in qemu no trouble at all and I captured those screenshots above. Very exciting! It was fun reading the old code even with all the comments I can barely remember how it all worked.

You can see the keyboard scan codes being shown and two counters that are incremented by timer interrupt handlers. I’m not sure why I used the VMware logo to make the background image, but obviously I was pretty excited about VMware at the time. I can remember the first time I saw that booting on my actual PC.

Anyway, I put it [up on bitbucket](https://bitbucket.org/jim_mussared/os) maybe one day I’ll pick it up again…
