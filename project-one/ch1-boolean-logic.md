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
