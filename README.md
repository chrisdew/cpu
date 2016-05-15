Homebrew CPU
============

These are just thoughts about a homebrew CPU that I will never get round to building...



Registers
---------

A limited set of programmer-visible registers.

16 bit registers:

```
OP - instruction being executed
PC - program counter
SP - stack pointer
X2 - base pointer (C) or return stack pointer (FORTH)
X3
X4
X5
Y0
Y1
W0
W1
W2
```

One bit registers:

```
HP - hop
```


Bus Widths
----------

```
16 bit data bus
16 bit address bus
```


Instruction Set
---------------

To be anywhere near practical to build, the CPUs instruction set must be tiny.
```
NOP/FETCH    000000000 0000000
EXECUTE      xxxyyznww caaarrr 
```

Some of the more useful instructions are:
```
NOP/FETCH    PC' <- PC + 1 + HP, OP' <- [PC']
ADD          X3|X4|Y0|W0|W1 <- 0|Xn|-1 + 0|Yn|1
SUB          X3|X4|Y0|W0|W1 <- 0|Xn|-1 - 0|Yn|1
INV          X3|X4|Y0|W0|W1 <- ¬Yn
LD           X5|Y1|W2 <- [0|Xn|-1 + 0|Yn|1]
LD IMM       PC' <- PC + 1, X5|Y1|W2 <- [PC'] 
ST	     [0|Xn|-1 + 0|Yn|1] <- Wn
PUSH         SP' <- SP + 1, [SP'] <- Wn
POP          X5|Y1|W2 <- [SP]
             SP' <- SP - 1
MV           X5|Y1|W2 <- Wn
MV           X3|X4|Y0|W0|W1 <- Xn|Yn
CMP          HP <- Yn > Xn
JPR	     PC' <- PC + Yn
CALL         W0' <- PC + 4 // FIXME, no space for literal offsets
             SP' <- SP + 1, [SP'] <- W0
             PC' <- Yn
RET          X5' <- [SP]
             SP' <- SP - 1
             PC' <- X5
```


Instruction Encoding
--------------------

```
xxx src
000 PC
001 SP
010 X2
011 X3
100 X4
101 X5
110 0xFFFF
111 0x0000
```

```
yy src
00 0x01
01 Y0
10 Y1
11 0x0000
```

```
z src
0 HP
1 0
```

```
n invert X and Z
0 false
1 true
```

```
ww src
00 --
01 W0
10 W1
11 W2
```

```
c dst
0 --
1 HP
```

```
aaa dst
000 PC
001 SP
010 X3
011 X4
100 Y0
101 W0
110 W1
111 --
```

```
rrr dst
000 OP
001 X5
010 Y1
011 W2
100 -- normal
101 RESERVED
110 RESERVED
111 -- and signals an interrupt
```

State Machine
-------------

This design is simplified by having no microcode or state machine.

The instruction coding directly drives the output-enable and latch controls on the registers.

The instruction coding is chosen such that 0x0000 encodes the fetch operation, which is:
```
PC' <- PC + 1 + HP, OP' <- [PC + 1 + HP]
```

The first instruction of an interrupt:
```
SP' <- SP + 1, [SP + 1] <- PC
```

Causes the subsequent fetch to be replaced with:

```
PC' <- 0xFFFF + 0 + 0, OP' <- [0xFFFF + 0 + 0] 
```
