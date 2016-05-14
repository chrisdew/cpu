Homebrew CPU
============

These are just thoughts about a homebrew CPU that I will never get round to building...



Registers
---------

A limited set of programmer-visible registers.

RZ - reads as zero - ignores writes
PC - program counter
SP - stack pointer
R3
R4
R5
R6
R7



Bus Widths
----------

16 bit data bus
16 bit address bus


Instruction Set
---------------

To be anywhere near practical to build, the CPUs instruction set must be tiny.

NOP                  00000000 00000000
MV  Rd Rs            001 ddd sss
LD  Rd [Ra]          010 ddd aaa
ST  [Ra] Rs          011 aaa sss
ADD Rr Rc Rx Ry Rz   110 rrr cc xxx yyy zz
SUB Rr Rb Rx Ry Rz   111 rrr bb xxx yyy zz


State Machine
-------------

000 - fetch
001 - move
010 - load
011 - store
100 - add
110 - sub


