The basic computer has three instruction code formats. Each has 16 bits. 

![[Pasted image 20231010083752.png]]

The register reference instructions are identified by opcode 111 and a 0 for bit 15. The I/O instruction is recognized by opcode 111 and I bit 1. The I is technically a mode bit but not used as one when the opcode is 111. 

Since 3 bits of opcode allows for only 8 instructions, this feature of using 111 opcode allows us to expand the instruction set, since the address space is unoccupied in these times. 

For a basic computer **a total of 25 instructions are defined as the basic instruction for a computer.**

![[Pasted image 20231010084344.png]]
**x** **here means address bits**. 

### Instruction set completeness
A set of instructions is said to be complete if : 
1. It has arithmetic, logic and shift instructions
2. Instruction for moving information to and from memory and processor register
3. Program control instruction along with status check instructions
4. Input and output instructions

The function of each instruction and the microoperations needed for their execution are present in next to next section, we must first study timing and control.

Next → [[Timing and Control]] 