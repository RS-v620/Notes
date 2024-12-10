Inside the #controlMemory there exists a defined order in which microinstructions are stored. They are stored in groups where each group specifies a #routine.  These routines correspond to each computer instruction. 

We elucidate the steps that control unit goes through to execute each individual computer instruction.
1. An initial address is loaded in the control address register at power on. This address usually stores the first instruction that initiates microinstruction fetch.
2. The control memory now must go through the routine that determines the effective address of the operand. A machine instruction may have bits that specify the specific addressing mode. This requires a branching microinstruction which depends on the specific addressing bits. At the end the address of the operand is available in the memory address register.
3. Now microoperations that execute the instruction fetched from memory must be generated. These are determined by the opcode part of the instruction. Each instruction has its own microprogram stored in control memory. This transformation from instruction to a #routine in #controlMemory is called #mapping.  
4. When execution of the instructions is complete, control must return to fetch routine. This is accomplished by executing an unconditional branch microinstruction to the first address of the fetch routine.

The following block diagram illustrates the hardware required for next microinstruction selection and the control memory. 
The microinstruction in the control memory contains a set of bits to initiate microoperations in computer registers and other bits to specify the method by which the next address is obtained. The diagram shows four different paths from which the #controlAddressRegister #CAR receives the address. A special register is used to store the return address, #SBR.
![[Pasted image 20231126130608.png]]
The incrementer increments the content of the control address register by one, to select the next microinstruction in sequence. Branching ( like in #JUMP) is achieved by specifying the branch address in one of the fields of the microinstruction. Conditional branching( like in #JNE or #JCS) is obtained by using part of the microinstruction to select a specific status bit in order to determine its condition. An external address is transferred into control memory via a mapping logic circuit. The return address for a subroutine is stored in a special register whose value is then used when the microprogram wishes to return from the subroutine.
## Conditional Branching
The branch logic as in figure above provides decision-making capabilities in the control unit. The status bits of the processor together with the field in the microinstruction that specifies a branch address control the conditional branch decisions generated in the branch logic.

## Mapping of Instruction
A special type of branching exists when a microinstruction specifies a branch to the first word in control memory where a microprogram routine for an instruction is located. The status bits in this type of branch are included in the instruction. Example can be, let the branch address be decided based on the following architecture: 
![[Pasted image 20231126191309.png]]
This allows four  microinstructions to be stored in the control memory corresponding to each computer instructions. Further any leftover space in the #controlMemory can be used if the space seems to be insufficient.

Another way of achieving this is by using am external ROM, whose sole job is to procure the opcode and generate the address. As mentioned before, ROM is just a combinational circuit that doesn't require a clock signal.

Finally, a third way is to use a #PLD or **Programmable logic device** which is same as a ROM, built of AND and OR gates which can be permanently programmed. 

## Subroutines
Subroutines are programs that are used by other routines to accomplish a particular task. Microinstructions that use subroutines hence need a register that stores the return address of a subroutine call and restore the address when the subroutine finishes. This register is the #subroutineRegister #SBR. The best way to structure a register file that stores addresses for subroutines is to organize the register in a LIFO stack.

Next → [[Microprogram Sequences]]