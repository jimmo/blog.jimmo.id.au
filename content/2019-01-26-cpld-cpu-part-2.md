Title: CPLD CPU: Part 2, Simulation
Date: 2019-01-26 22:50:00 +1100
Category: Projects

This is part 2 of a [series about building a CPU out of small components implemented in CPLD](/2019/01/26/cpld-cpu-part-1.html). See that page for links to the other parts.

So at this stage I'd decided that the XC9572XL CPLD in the VQ44 package looked like a good candidate, so 34 I/O pins per CPLD. This should give plenty of pins for a data bus, address bus, and various control signals. I wasn't sure what I wanted to do about addressing RAM vs ROM, Harvard vs Von Neumann, etc, or how to make GPIO work (ports or memory mapped). And no strong opinions on instruction set other than it was going to be extremely RISC (to simplify implementation) and that I have a mild preference for jumps rather than skips. More on all of that later.

In order to experiment, I decided to build a simulator. If I just wanted to explore the instruction set, then building an assembler and an emulator would have been sufficient, but I actually wanted to simulate the CPLDs talking to eachother. There are countless tools out there to do this, and one really good option would have been to implement each CPLD in VHDL and use one of the VHDL simulators to do this. But due to equal parts stubbornness, ignorance, and curiosity, I decided it would be fun to make my own logic simulator in Python.

The basic idea is fairly simple. There's a series of nets (i.e. one group of pins from different components all electrically connected together). Whenever one of the pins "drives" the net, the other pins see that value. This causes the components to execute their logic, which might cause other pins to change. Eventually the whole thing converges to a stable state. It's an error to have multiple drivers on the same net at the same time, and if it doesn't converge, that indicates a logic error.

More concretely:
 - **Components** represent individual ICs in the circuit
 - Components have **signals** which represent one or more pins that they can either make **high inpedance** (i.e. input mode), or **driven** (i.e. set to high or low). Grouping pins into signals lets the logic work with bit vectors instead of just bits.
 - Components can also have state (implmented as member variables). Conceptually this is like having a register built-in to the IC.
 - Signals can be subsetted to make **signal views** (i.e. to pull off the high four bits of the address bus).
 - Signals (or signal views) can be connected, which connects their corresponding pins together.
 - When a signal is driven, all other connected signals' pins are updated, and a method is called on the affected components.
 - This method uses the component state and pin values to drive other pins and update the state.

Here's a simple example showing an AND gate:

```python
from sim import *

class AndGate(Component):
def __init__(self, width=1):
  super().__init__('AND')
  self.a = NotifySignal(self, 'a', width)
  self.b = NotifySignal(self, 'b', width)
  self.out = Signal(self, 'out', width)

def update(self, signal):
  self.out <<= self.a.value() & self.b.value()

g1 = AndGate(4)

g2 = AndGate(2)
g2.a += g1.out
g2.b <<= 1

g1.a <<= 9
g1.b <<= 11

print(g2.out)
```

Something like a register with write-enable and output-enable control pins would look like:

```python

class Register(Component):
def __init__(self, name, width=8):
  super().__init__(name)
  # This is internal state.
  self.v = 0

  # External pins:
  # Data in/out
  self.data = Signal(self, 'data', width)
  # Write enable (active high)
  self.we = NotifySignal(self, 'we', 1)
  # Output enable (active high)
  self.oe = NotifySignal(self, 'oe', 1)
  # Current state (regardless of OE)
  self.state = Signal(self, 'state', width)

def value(self):
  return self.v

def update(self, signal):
  if self.we.had_edge(0, 1):
    self.v = self.data.value()
    trace('{} = 0x{:04x}'.format(self.name(), self.v))
  self.state <<= self.v
  if self.oe.value():
    self.data <<= self.v
  else:
    self.data <<= None

r = Register('X')
r.data <<= 7
print(r.value())
r.we <<= 1
print(r.value())
```

The examples get more interesting when you add some sort of clock or changing input:

```python
g1 = AndGate(4)
g2 = AndGate(2)
g2.a += g1.out[0:2]

for i in range(16):
for j in range(16):
  for k in range(4):
    g1.a <<= i
    g1.b <<= j
    g2.b <<= k
    print(g2.out)
```

In the case of a CPU simulation, this would be a clock signal toggling between 0 and 1.
This became my "offline" project. i.e. any time I had my laptop with me with no other distractions or internet access. Mostly this meant I worked on it on airplanes, which is neither a great work environment nor something I do very often. So it took a while! The implementation is fairly simple, there's a couple of small optimisations but for the most part there are no complicated algorithms or computer science. I implemented the simulator and the components for a basic CPU at the same time, and quite clearly remember both the flight in early 2017 where it first did something that looked like the program counter incrementing and, much later in 2017 (New Zealand for Christmas), a flight where the CPU executed some basic code.

The implementation [is on GitHub](https://github.com/jimmo/cpld-cpu/blob/master/pysim/sim.py). Most of the code is dealing with the (somewhat questionable, but more VHDL-familiar) operator overloading and the implementation of some basic components (RAM, ROM, Register, Adder, Multiplexer, etc). Signals manage a collection of pins, which know which net they're on, and how to propogate changes to other pins.

The distinction between `Signal` and `NotifySignal` is an optimisation -- only `NotifySignal` pins will cause the `update` method on their parent component to be called. Any output-only signals, or an input signal that only gets read on a transition of another pin will be just `Signal`.

Because this is written in Python, there's nothing stopping arbritrary code being performed in the `update` method. The idea is that anything you write should be logically the same as what can be expressed in VHDL.

You can see some simple demos [in the demo directory](https://github.com/jimmo/cpld-cpu/tree/master/pysim/demo). It's not fast, but it's easily capable of simulating digital circuits of 74xx ICs.

Up next: Our first attempt at a simple CPU.
