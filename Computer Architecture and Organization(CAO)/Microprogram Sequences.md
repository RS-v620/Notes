>[!disclaimer] This has been taken from a section headed as **Microprogram Example**, no other book has this topic. A quick comparison online yielded the same results as the book so I am assuming this is what the syllabus setter aimed at.

After establishing a the configuration of a computer and its microprogrammed control unit, it's the designer task to generate the microcode for the control memory. This is #microprogramming and is a process similar to conventional machine language programming. We start by using a model of simple digital computer which is to be microprogrammed.

### Computer Configuration
The computer, as shown below, consists of two blocks of memory units: a main memory for storing instructions and data, and a control memory for storing microprogram. Four registers are associated with the processor unit and two with the control unit. The processor registers are #programCounter #PC, #addressRegister #AR, #dataRegister #DR and #accumulator #AC. The control unit has a #controlRegister #CAR and a #subroutineRegister #SBR . The control unit and its registers area arranged as microprogrammed control unit as described in previous articles.
![[Pasted image 20231126195244.png]]

The computer instruction format is as shown below: 
![[Pasted image 20231126202047.png]]
It consists of 1 bit #indirectAddress ing mode, 4 bit #opcode and a 11 bit address field. The 4 bits of opcodes allow for 16 instructions which must all be microprogrammed. We look at 4 of them.

Next → [[MicroInstruction Formats and Software Aids]]
