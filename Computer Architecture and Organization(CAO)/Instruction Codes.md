The internal organization of a digital system is defined by the sequence of microoperations it performs on data stored in its registers. 

A computer instruction is a binary code that specifies a sequence of microoperations for the computer. Instruction codes together with data are stored in memory. The computer reads each instruction from memory and places it in a control register. The control then interprets the binary code of the instructions and proceeds to execute it via a sequence of microinstructions. 

### Instruction Code
#instructionnCode
Is a group of bits that instruct the computer to perform a specific operation. It is usually divided in parts with their own meanings.

#### Operation Code
#opcode #operationCode
The operation code of an instruction is a group of bits that define the operation. It consists of n bits for $2^n$ total operations. 

We must recognize the relationship between a computer operation and a microoperation. An operations is part of an instruction stored in computer memory. It is a binary code that tells the computer to perform a specific operation. The CU receives the instruction from memory and interprets the operation code bits, followed by issuing a sequence of control signals to initiate microoperations in internal computer registers. This means that the operation code is  #macrooperation.

### Stored Program Organization
The simplest computer organization is to have a single processor register and instruction code format with two parts. One part hosts the #opcode, and the other has address of operand. The memory is divided into instruction sector and operand sectors.

![[Pasted image 20231010075923.png]]
Appropriate number of bus line s are used to address all possible memory locations. 
The single processor register is called the #accumulator. 

### Indirect address
It is convenient to use the address bits of an instruction code to store the actual operand. This is called #immediateOperand . When the second part has an address it is called #directAddress.  The third possibility is that the second part stores and address, and at this address is another address that stores te operand, this is called #indirectAddress. 

##### Effective address
#effectiveAddress is the address of the operand in a computer instruction or the target address in a branch-type instruction.

A single bit is used to identify whether the address is direct or indirect in the instruction.
![[Pasted image 20231010081934.png]]
0 for a direct address, and 1 for an indirect address, at position 15
# Computer Registers
#registers 

Are needed to perform processes, fetch instructions and operands, program pointer, processor register, and temporary register

|S. No| Register Symbol | Number of bits | Register Name        | Function                     |
|------| --------------- | -------------- | -------------------- | ---------------------------- |
| 1| DR              | 16             | Data Register        | Holds memory operand         |
|2| AR              | 12             | Address register     | Holds address for memory     |
|3| AC              | 16             | Accumulator          | Processor Register           |
|4| IR              | 16             | Instruction register | Holds instruction code       |
| 5|PC              | 12             | Program counter      | Holds address of instruction |
| 6|TR              | 16             | Temporary register   | Holds temporary data         |
| 7|INPR            | 8              | Input Register       | Holds input character        |
| 8|OUTR            | 8              | Output Register      | Holds output character                             |


#### Common Bus system
#bus #commonBusSystem
The basic computer has 8 registers, a memory unit and a control unit. Paths must be provided to transfer information from one register t another and between memory and registers. An efficient scheme to make these connections is to use a bus.
There are select variables controlled by the CU that operate the bus system to which 7 registers are connected. 
When these lines are on, the particular register that has itself selected receives the information from its enabled *LD*(load) input. 

![[Pasted image 20231010083012.png]]
#inr #clr #ld 
Five registers have three control inputs: *LD, INR(increment), and CLR (clear)*. 

Input of any register can be applied to the bus and an operation can be performed in the ALU during the same clock cycle. The clock transition at the end of a cycle transfers the content of the bus into the designated destination register and the output of the adder and logic circuit into the AC. 
i.e. $$DR ← AC \;\;\;and\;\;\;AC ← DR$$
can be done at the same time.

Next → [[Computer Instructions]] 