---
layout:	post
title:	"Two weeks of projects: Day 3"
date:	2016-04-19
---

  First weekday and the first deviation from the project list while I spend time learning Go. This of course meant setting up a dev environment, so down the Emacs Lisp rabbithole. All set up now though and so far the Go+Emacs set up is probably one of the nicest workflows I’ve ever experienced for any language.

There are lots of guides out there that go into a lot of detail, but here’s what I condensed it down to:

You’ll need to have the various packages installed — I use MELPA, with a handy function to install all my required packages on a new machine.

I have these in my .emacs (which is synced across machines from a github repo).

Go uses tabs! It’s like being back in Windows land.

GOPATH initially confused me. It seems awesome if you’re working entirely in Go and each repo is either a single binary or a library. And if all you do is write Go — why would I want to put my non-Go code inside $GOPATH? But it turns out the secret to happiness is just to set GOPATH=$HOME and get on with it. Thanks, Sam, for the reassurance!

Now that I’ve read the book, time to start writing an assembler. Last time I did this (in C++) I was super lazy and just did something like:

* For each line
* Remove everything from a ’;’ onwards. (Comments)
* Split by space and comma.
* Now you have opcode plus optional args.
* And if the opcode ends with a ’:’ it’s a label.

This time, a real lexer and parser. Years ago, I attended [this talk by Rob Pike in Sydney about writing a lexer in Go](https://www.youtube.com/watch?v=HxaD_trXwRE). So I implemented that. I’m pretty happy with the result, but it feels weird to be effectively duplicating a lot of [text/template/lex.go](https://go.googlesource.com/go/+/master/src/text/template/parse/lex.go) from the standard library.

Also I spent a bunch of time today on random stuff around the house. Tidying, cleaning, cooking, etc. And jogging!
