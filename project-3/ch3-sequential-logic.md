# Sequential Logic

Everything we've done to this momenet does not track state - or memory. It takes inputs and calculates outputs combinatorially.

We call chips that can perserve data overtime sequential.

Keeping state is inherently dependent on time. You can only remember now what you chose to remember before.

So to build chips that help us remember state, we must understand time.

## Background

We typically build clocks from a continuous signal of alternating signals.

The signal goes high, then low, then high again on a set interval. Each alternation high-low pairing is a cycle. Each cycle is one unit of time.

### Flip Flops

We're going to focus on DFFs, or data flip-flops.

A DFF is a single bit data input and a single bit data output. It has a clock input that continuously changes according to the master clock's signal.

Together, these two signals allow the DFF to perform time-based behavior.

```
DFFout(t) = in (t-1)
```

In other words, the DFF outputs the input value from the previous time unit.

### Registers

A DFF can only remember it's previous state. But if we want to maintain that state for more than one clock signal, we have to find an alternative storage method.

We can build registers for this. By combining together a mux and a DFF, we have a way a method of maintaining memory over time. The DFF is wired into the mux as it's second input signal. Changing the mux from 0 to 1 would allow us to take in a new input signal.

And at any time we can read the currently stored register bit.

This is elegant and convenient. Now we have a single load bit that we can set whenever we want to store a new value on a given set of registers.

### Memories

Once we can build registers, we can start to build memory banks of arbitrary length.

This is where we start to encounter our dear friend RAM, or random access memory. RAM has a fundamental property hidden in that name - you should be able to access it's memory at random to get randomly chosen registers.

To handle this constraint, we give each register in RAM an address. Then we build a gate that, given an address, can ouput the register at that address.

RAM typically has three major inputs.

1. address of the register
2. the data input
3. the load bit

If load is 0, this is a read op. If 1, it's a write op.

### Counters

A counter is a chip show state is an integer that increments every time unit.

CPUs often contain counters whose output is the address of the instruction that should be executed next in the current program.

A counter chip can be implemented like a normal register with a constant being added through combinatorial logic. They also have additional pins for resetting teh count, loading a new constant or decrementing.

### Timing

Any chip whose functionality changes based on the clock is a sequential chip.

Think of the ALU performing an addition on x and y. X is on-pin currently, provided by some logic gate already. Y is coming from RAM. It may take one or more clock cycles for y to get there.

In the meantime, x+y from the ALU is outputting garbage.

The nice thing though, is our output registers will be sequential. We can tell them when to update with a load pin. We can also make sure that, given our design, the clock cycles are always slightly longer than the time it takes to retrieve something from memory.

In general, the more moving pieces we add, the more timing, and synchronization matter.

## Specifications

Chip name: DFF
Inputs: in
Outputs: out
Function: out(t)=in(t-1)
Comment: This clocked gate has a built-in
implementation and thus there is
no need to implement it.

All DFFs are connected to master clocks. At the beginning of the clock cycle, all DFFs in the computer increment to the value they were given during the last clock cycle. At other times, their inputs are "latched" meaning that they do not react to changes in inputs.

Chip name: Bit
Inputs: in, load
Outputs: out
Function: If load(t-1) then out(t)=in(t-1)
else out(t)=out(t-1)

Chip name: Register
Inputs: in[16], load
Outputs: out[16]
Function: If load(t-1) then out(t)=in(t-1)
else out(t)=out(t-1)
Comment: "=" is a 16-bit operation.

Chip name: RAMn // n and k are listed below
Inputs: in[16], address[k], load
Outputs: out[16]
Function: out(t)=RAM[address(t)](t)
If load(t-1) then
RAM[address(t-1)](t)=in(t-1)
Comment: "=" is a 16-bit operation.
The specific RAM chips needed for the Hack platform are:
Chip name n k
RAM8 8 3
RAM64 64 6
RAM512 512 9
RAM4K 4096 12
RAM16K 16384 14
