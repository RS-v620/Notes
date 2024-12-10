Major components of a digital computer are :
* CPU: 
	1. Control Unit
	2. Arithmetic Logic Unit
	3. Registers
* Memory
* I/O

The complexity of a digital system is derived from the number of sequences of microoperations that are performed. There are two methods of implementing Control Units:
1. **Hardwired Control** : It has fixed instructions, fixed logic blocks, arrays, encoders and decoders etc. They are fast but expensive, complex and have no scope of adding new instructions. All #RISC CPUs are hardwired. Eg. Intel 8085
2. **Microprogramming Control** : Present in all #CISC, the basic principle is to control microoperation sequences that are executed in the Control Unit. Egs. Intel 8080

A control variable/ control function is a binary variable capable of activating a logic function either at 1 or 0. Control variables can be represented by strings of 1s and 0s called a #controlWord.  A control unit whose binary control variables are stored in memory is called a #microprogrammedControlUnit. Each word in control memory contains within it a microinstruction. A sequence of microinstructions constitute a #microprogram. These control instructions are stored in ROM usually and are designed during manufacturing only. Some more advanced control units provide programmable microinstructions to be loaded into the control memory, called a #dynamicMicroprogramming #CU.

A computer that is microprogrammed control invariably has two memories : a main memory and a control memory. Each machine instruction, provided by the user initiates a set of microinstructions in the CU. These microinstructions generate the microoperations to fetch the instruction from the main memory, to evaluate the effective address, to execute the operation specified by the instruction, and to return the control to the fetch phase in order to repeat the cycle for the next instruction.

The general configuration of a microprogrammed control unit is demonstrated in the block diagram below. The control memory is a ROM. The control memory address register specifies the address of the microinstructions and the control data register holds the microinstruction read from the memory. 
![[Pasted image 20231126121256.png]]
The microinstructions have some reserved bits that are used to deduce the next address of the instruction, which is often done by a dedicated Next Address generator circuit. Remember that each #microinstruction can generate more than one #microoperation.

The next address generator is also called a #sequencer. It has functions such as :
1. Incrementing the address register
2. Loading into the control address register an address from control memory
3. Transferring an External address
4. Loading the initial address to start the control operations.

The data register is sometimes called #pipelineRegister, It allows for the execution of the microoperations specified by the control word simultaneously with the generation of the next microinstruction. This requires a two-phase clock, with one applied to the address register and the other to the data register. Though the systen can operate without a control data register by obtaining both microinstruction and next address from the #controlMemory 

It is imperative to know that only the address register requires a clock pule in the CU control registers. The control data register obtains data from the ROM(Control Memory) which does not require a read signal and operates as a *combinational circuit*. Same is the story with the #sequencer .

The main advantage of microprogrammed control is that the hardware needs to be designed only once. We can change the control memory contents to accommodate any upgrades.

Next → [[Address Sequencing]]
