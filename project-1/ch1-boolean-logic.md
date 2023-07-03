# Boolean Logic

Our core unit of abstraction is a logic gate.

We're going to start with a NAND, a universal gate (so is NOR iirc), then build the others.

We'll have the standard set of gates at the end of the chapter.

In chapters 2 and 3, we'll use these primitive gates to build our processor and storage.

## Background

We're about to speedrun the first two years of ECE.

To understand logic gates, we need to understand what they're implementing - Boolean functions and Boolean algebra.

Just like Boolean algebra, we can combine together Boolean gates and thus our logic gates into larger, more complex pieces of functionality.

### Boolean Algebra

A Boolean is a binary value, true/false. 1/0, yes/no, on/off.

A Boolean function, then, is a function that operates on binary inputs and produces binary outputs.

When we want to represent a Boolean function, we lay it out in a truth table.

A truth table lays out all possible binary inputs, alongside the outputs on the function.

```
x y | f(x,y)
0 0 | f1
0 1 | f2
1 0 | f3
1 1 | f4
```

For a given function's n number of binary inputs, there are 2^n possible tuples of combinations. Above we have 2 inputs. 2^2 = 4. Each row is a tuple, and thus we have 4 tuples.

#### Basic Ingredients

There are common expressions in Boolean algebra.

We have the basic ingredients:

- AND -> f(1, 1) = 1
- OR -> f(0, 1), f(1, 0), f(1, 1) = 1
- NOT -> f(1) -> 0, f(0) -> 1

We have symbolic representation of these.

- x AND Y -> xy or x \* y
- x OR y -> x + y
- NOT x -> ~~x~~. (Note to self, it's an overline character, but those are hard to type quickly in MD so using the strike notation instead)

To walk through an example:

f(x, y, z) = (x + y) \* ~~z~~

would be (x OR y) AND (NOT Z)

which would have a truth table of:

| x y z | f(x,y) |
| ----- | ------ |
| 0 0 0 | 0      |
| 0 0 1 | 0      |
| 0 1 0 | 1      |
| 0 1 1 | 0      |
| 1 0 0 | 1      |
| 1 0 1 | 0      |
| 1 1 0 | 1      |
| 1 1 1 | 0      |

#### Canonical Representation

Given a truth table, how do we assemble a Boolean function, or it's canonical representation?

Finding canonical representation

1. start with the rows that evaluate to TRUE
2. AND together all the elements of that row
3. OR together all the terms of the different tuples

Let's try that on the above table.

Step One and Two:

- f(0 1 0) = 1 -> ~~x~~ \* y \* ~~z~~
- f(1 0 0) = 1 -> x \* ~~y~~ \* ~~z~~
- f(1 1 0) = 1 -> x \* y \* ~~z~~

Step Three:
f(x, y, z) = f(0 1 0) + f(1 0 0) + f(1 1 0)

f(x, y, z) = (~~x~~ \* y \* ~~z~~) + (x \* ~~y~~ \* ~~z~~) + (x \* y \* ~~z~~)

Every Boolean function or set of functions yields a truth table. Every truth table can be assembled into simple canonical representations with only AND, OR and NOT.

**That implies that every boolean expression can be written in terms of AND, OR or NOT.**

And so, if we can build representations of these three gates, we can build system that has a foundation of Boolean logic.

#### Two-Input Boolean Functions

We know that for any given n amount of binary inputs, there are 2^n binary outputs.

But can we also define the number of functions there are that can operate on the n binary inputs?

Turns out, yeah.

Sit back and think about it with one binary variable.

We have one binary input to our function. There 2^1 binary outputs. 2^1 is 2.

| x   | f(x) |
| --- | ---- |
| 0   | out1 |
| 1   | out2 |

But for each input, there are two potential outputs, 1 or 0. Another way of saying that is for each set of inputs, there are two potential outcomes, 1 or 0.

That would imply there are 2^(2^n) possible functions for any given set of n binary inputs.

2^2^n is 4.

So the space of possibilites function map looks more like this:

| x   | f(x) | Function type                  |
| --- | ---- | ------------------------------ |
| 0   | 0    | Constant 0, or always FALSE    |
| 1   | 0    |                                |
|     |      |                                |
| 0   | 1    | Constant 1, or always TRUE     |
| 1   | 1    |                                |
|     |      |                                |
| 0   | 1    | NOT X, always invert x         |
| 1   | 0    |                                |
|     |      |                                |
| 0   | 0    | Passthrough x, always return x |
| 1   | 1    |                                |

You can think about this like a relay station. Depending on what x you give me, I have four ways to respond consistently. I can always pass on your message. I can always invert your message. Or I can always pass along one of two of the same messages regardless of your message

Now if we try this with two inputs, we end up with 16 possible functions that we can build.

| x   | y   | f(x,y) | Name                                             |
| --- | --- | ------ | ------------------------------------------------ |
| 0   | 0   | 0      | Always False                                     |
| 0   | 1   | 0      |
| 1   | 0   | 0      |
| 1   | 1   | 0      |
|     |     |        |
| 0   | 0   | 1      | Always True                                      |
| 0   | 1   | 1      |
| 1   | 0   | 1      |
| 1   | 1   | 1      |
|     |     |        |
| 0   | 0   | 0      | x                                                |
| 0   | 1   | 0      |
| 1   | 0   | 1      |
| 1   | 1   | 1      |
|     |     |        |
| 0   | 0   | 0      | y                                                |
| 0   | 1   | 1      |
| 1   | 0   | 0      |
| 1   | 1   | 1      |
|     |     |        |
| 0   | 0   | 0      | AND                                              |
| 0   | 0   | 0      |
| 1   | 0   | 0      |
| 1   | 1   | 1      |
|     |     |        |
| 0   | 0   | 0      | OR                                               |
| 0   | 1   | 1      |
| 1   | 0   | 1      |
| 1   | 1   | 1      |
|     |     |        |
| 0   | 0   | 1      | NAND                                             |
| 0   | 1   | 1      |
| 1   | 0   | 1      |
| 1   | 1   | 0      |
|     |     |        |
| 0   | 0   | 1      | NOR                                              |
| 0   | 1   | 0      |
| 1   | 0   | 0      |
| 1   | 1   | 0      |
|     |     |        |
| 0   | 0   | 0      | XOR                                              |
| 0   | 1   | 1      |
| 1   | 0   | 1      |
| 1   | 1   | 0      |
|     |     |        |
| 0   | 0   | 1      | EQUIVALENCE. (x AND y) OR ((NOT x) AND (NOT y) ) |
| 0   | 1   | 0      |
| 1   | 0   | 0      |
| 1   | 1   | 1      |
|     |     |        |
| 0   | 0   | 1      | NOT x                                            |
| 0   | 1   | 1      |
| 1   | 0   | 0      |
| 1   | 1   | 0      |
|     |     |        |
| 0   | 0   | 1      | NOT y                                            |
| 0   | 1   | 0      |
| 1   | 0   | 1      |
| 1   | 1   | 0      |
|     |     |        |
| 0   | 0   | 0      | X AND (NOT y)                                    |
| 0   | 1   | 0      |
| 1   | 0   | 1      |
| 1   | 1   | 0      |
|     |     |        |
| 0   | 0   | 0      | (NOT X) AND y                                    |
| 0   | 1   | 1      |
| 1   | 0   | 0      |
| 1   | 1   | 0      |
|     |     |        |
| 0   | 0   | 1      | (NOT y) OR x                                     |
| 0   | 1   | 0      |
| 1   | 0   | 1      |
| 1   | 1   | 1      |
|     |     |        |
| 0   | 0   | 1      | (NOT x) OR y                                     |
| 0   | 1   | 1      |
| 1   | 0   | 0      |
| 1   | 1   | 1      |
|     |     |        |

Whew what a big table!

Another thing not mentioned in the book is that there are basically 2^2^2^n tuples to represent 2^2^n functions that can operate on 2^n inputs

So many 2s!

#### NAND and NOR

NAND and NOR have interesting properities.

You can build AND, OR and NOT from them.

For ex, x OR y = (x NAND x) NAND (y NAND y)

**And if we can build AND, OR and NOT from NAND or NOR, it logically follows that we can build all of Boolean algebra in terms of NAND or OR, or a combination of both.**

### Gate Logic

A Gate is a device that performs a boolean function.

If a function, f has n binary inputs and m binary outputs, then the gate for that function has n input pins and m output pins.

Given there is a direct mapping between Boolean algebraic functions and Logic Gates, we can also say that a complex logic gate can be constructed out of simpler logic gates, just as we can build complex Boolean functions from simpler ones.

Our simplest gates are transistors.

Computers use electricity to represent and transmit binary data, but any alternative technology that abstract the functionality of a transistor can do the same. Since logic gates are composable, we'll use "gate" to refer to lower level gate combinations and "chips" to refer to more complex gate combinations.

The idea that Logic Gates don't depend on the implementation of a switch is important. It means we have our first opportunity to introduce an abstraction layer!

Ooooh buddy we love an abstraction layer.

We do not care how the Boolean logic is implemented, only that it meets the qualities of Boolean logic. That means we can build a computer from any system, any API, that is given to us that represents Boolean logic.

Remember the book the 3 Body Problem? In the book, an alien species create a living computer by having folks stand in a formations with flags, and each person had instructions on whether to raise or lower their flag depending on some other flag’s signal. Creating Boolean logic from people in a field lol.

It's all abstraction. And now that we're freed from the implementation via the abstraction, we can focus on the abstract idea of "gates" as a black box.

#### Primitive to Composite Gates

We already know AND, OR and NOT gates. These are our primitive gates.

We can create more complex gates by streaming together the outputs of these primitives.

An AND gate with 3 inputs is `f(x, y, z) = x AND y AND z.` But that can be broken down into and evaluation order of `f(~) = (x AND y) AND z`. Or, if we write it like a function call. `f(~) = AND(AND(x, y), z)`

Or, in order of evaluation:

1. x AND y = v1
2. v1 AND z = f(~)

In that sense, the combination of these two AND gates into a three input AND gate creates another black box abstraction. We can represent the 3 pin AND gate with a single gate, and not care about the representation below, as long as that representation holds true to the abstraction.

This is how we perform gate logic, or logic design.

Logic design is the art of connecting gates in order to implement more complex functionality; constructing composite gates from primitives. As I said above, these composite gates get the "outside" appearance of our primitives, so they themselves can be used as primitives for even more complex composite gates.

But everything boils back down to AND, OR and NOT baby.

The difference between a Logic Gate's black box "outside" appearance and the "inside" workings is what we'll call "interface" vs "implementation".

Let's think about a XOR gates implementation and interface.

The interface is simple. Two inputs, one output. And a truth table that looks like:

| x   | y   | XOR(x, y) |
| --- | --- | --------- |
| 0   | 0   | 0         |
| 0   | 1   | 1         |
| 1   | 0   | 1         |
| 1   | 1   | 0         |

Using our canonical representation function, we can find the equation for this table.

`XOR(x, y) = (!x AND y) OR (x AND !y)`

In a LISPy format, this looks lik

```
XOR(x, y)
-> (OR (AND (NOT x) y) (AND x (NOT y)))
--> OR
----> AND
------> NOT
--------> x
------> y
----> AND
------> x
------> NOT
--------> y
```

That is a total of 5 gates in the implmentation, but still two inputs and one output for the interface itself.

The art of logic design boils down to "how do I fulfill this interface with as few gates as possible".

### Hardware Description Language

Instead of building a bunch of composite chips ourselves, we're going to accept another abstraction - a HDL, or a hardware description language.

We can pass HDL software to a simulator and model the chip in the computer's memory. We can use these simulations to test inputs and generate outputs.

We can also ascertain cost/speed/power requirements etc.

## Specification

Now we're going to go over a typical set of gates for different kinds of Boolean operations and how they're designed. We're going to start with a NAND gate and derive the others recursively.

### Basic Gates

Chip name: Not
Inputs: in
Outputs: out
Function: If in=0 then out=1 else out=0.

Chip name: And
Inputs: a, b
Outputs: out
Function: If a=b=1 then out=1 else out=0

Chip name: Or
Inputs: a, b
Outputs: out
Function: If a=b=0 then out=0 else out=1.

Chip name: Nand
Inputs: a, b
Outputs: out
Function: If a=b=1 then out=0 else out=1
Comment: This gate is considered primitive

#### Multiplexer

A multiplexer, or Mux, is a "selector" chip. It has three inputs, a, b and a selection bit. The selection bit chooses which output you want between a or b.

Chip name: Mux
Inputs: a, b, sel
Outputs: out
Function: If sel=0 then out=a else out=b.

Their logic gate looks like this:

| x y sel | f(x, y, sel) |
| ------- | ------------ |
| 0 0 0   | 0            |
| 0 0 1   | 0            |
| 0 1 0   | 1            |
| 0 1 1   | 1            |
| 1 0 0   | 0            |
| 1 0 1   | 1            |
| 1 1 0   | 0            |
| 1 1 1   | 1            |

#### Demultiplexers

In the same vein of multiplexers, demultiplexers are a "selector". They take in a single input and a selector, then return two output pins, a or b. The selector chooses which output pin inherits the input pin's signal.

Chip name: DMux
Inputs: in, sel
Outputs: a, b
Function: If sel=0 then {a=in, b=0} else {a=0, b=in}.

| x sel | fa(x, sel) | fb(x, sel) |
| ----- | ---------- | ---------- |
| 0 0   | 0          | 0          |
| 1 0   | 1          | 0          |
|       |            |            |
| 0 1   | 0          | 0          |
| 1 1   | 0          | 1          |

### Multibit Versions of Basic Gates

Typically computer architecture uses multi-bit arrays called buses. A 32-bit computer is called that because it can compute a Boolean operation on two 32-bit inputs.

The chip to perform this would have 2 32-bit input buses and one 32-bit output bus.

You can think of multibit gates as the scaled versions of their single-bit little brothers.

Chip name: Not16
Inputs: in[16] // a 16-bit pin
Outputs: out[16]
Function: For i=0..15 out[i]=Not(in[i]).
Comment: Invert every single input.

Chip name: And16
Inputs: a[16], b[16]
Outputs: out[16]
Function: For i=0..15 out[i]=And(a[i],b[i]).
Comment: AND each bitwise pair

Chip name: Or16
Inputs: a[16], b[16]
Outputs: out[16]
Function: For i=0..15 out[i]=Or(a[i],b[i]).
Comment: OR each bitwise pair

Chip name: Mux16
Inputs: a[16], b[16], sel
Outputs: out[16]
Function: If sel=0 then for i=0..15 out[i]=a[i]
else for i=0..15 out[i]=b[i].
Comment: Works exactly the same as single bit muxes, but with an entire bus.

(note this is not in the book, just me writing)
Chip name: Dmux16
Inputs: in[16], sel
Outputs: aout[16], bout[16]
Function: If sel=0 then for i=0..15 aout[i]=in[i]
else for i=0..15 bout[i]=in[i].
Comment: Works exactly the same as single bit demuxes, but with an entire bus.

### Multiway Gates

Multiway gates, on the other hand, scale up the number of inputs beyond two bits or buses.

Chip name: Or8Way
Inputs: in[8]
Outputs: out
Function: out=Or(in[0],in[1],...,in[7]).
Cmment: Performs an OR evaluation on all the bits in the bus.

#### Multiway Muxes

Multiway Muxes are described as "m-way, n-bit". A 4 way, 16 bit multiplexer takes in four separate 16 bit buses and selects between them.

The number of selection bits, or controller bits, now have to be scaled because we cannot represent 4 inputs with TRUE/FALSE. You can calculate the number of selection bits you need with k = log2(m), which is 2. That intuively makes sense. Our bits in each bus will only ever scale by 2. For every new pair inputs, we need one more selection bit. log2(m) deconstructs the 2^n n inputs we have into the number of pairs we can represent with one selection bit.

Multiway Mux
Chip name: Mux4Way16
Inputs: a[16], b[16], c[16], d[16], sel[2]
Outputs: out[16]
Function: If sel=00 then out=a else if sel=01 then out=b
else if sel=10 then out=c else if sel=11 then out=d
Comment: The assignment operations mentioned above are all
16-bit. For example, "out=a" means "for i=0..15
out[i]=a[i]".

Chip name: Mux8Way16
Inputs: a[16],b[16],c[16],d[16],e[16],f[16],g[16],h[16],
sel[3]
Outputs: out[16]
Function: If sel=000 then out=a else if sel=001 then out=b
else if sel=010 out=c ... else if sel=111 then out=h
Comment: The assignment operations mentioned above are all
16-bit. For example, "out=a" means "for i=0..15
out[i]=a[i]".

#### Demux

Okay same thing but for Dmuxes. We also describe them as "m-way, n-bit", except the implementation is flipped from the mux. We take in an n-bit bus and then route it to m possible n-bit output buses. We can calculate our required selection bits here again with k=log2(m) for the exact same reason.

Chip name: DMux4Way
Inputs: in, sel[2]
Outputs: a, b, c, d
Function: If sel=00 then {a=in, b=c=d=0}
else if sel=01 then {b=in, a=c=d=0}
else if sel=10 then {c=in, a=b=d=0}
else if sel=11 then {d=in, a=b=c=0}.

Chip name: DMux8Way
Inputs: in, sel[3]
Outputs: a, b, c, d, e, f, g, h
Function: If sel=000 then {a=in, b=c=d=e=f=g=h=0}
else if sel=001 then {b=in, a=c=d=e=f=g=h=0}
else if sel=010 ...
...
else if sel=111 then {h=in, a=b=c=d=e=f=g=0}.

## Implementation

In the coursework, we're not going to go implement NOT/AND/OR/XOR/MUX/DMUX and their multibit and multiway brethern.

This section contains hints to those exercises.

## Perspective

We've ignored several things in this chapter that are important.

1. You can build a computer from many combinations of these gates; you can build a computer completely from NOR gates! There are real tradeoffs for doing so, but they're beyond the scope of this course.
2. We've ignored all implementations of these chips and of their underlying transistors. This is the domain of ECE, and I should dive back into my old books to catch up on all that soon. Maybe before I move on.
3. We've also ignored efficiencies from the layout of chips and just assume connections can be made easily however we design things. In physical implementations of course, there are tradeoffs to be had about circuit routing.
