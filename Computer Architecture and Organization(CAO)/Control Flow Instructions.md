
## Branch Instructions
#B #BAL #BEQ #BNE #BPL #BMI #BCC #BLO #BCS #BHS #BVC #BVS #BGT #BGE #BLT #BLE #BHI #BLS
Used as 
```
B LABEL
```
This in unconditional branching, we also have conditional branching .
Some instructions have multiple names and are shown by placing them in the same cell.

| Branch      | Interpretation                 | Normal Uses                                                                      |
| ----------- | ------------------------------ | -------------------------------------------------------------------------------- |
| B or BAL    | Always branch                  | Always take the branch                                                           |
| BEQ         | Branch if Equal                | Comparison results in 0                                                          |
| BNE         | B if not Equal                 | Non equal comparison                                                             |
| BPL         | Branch if Plus                 | Result is +ve or 0                                                               |
| BMI         | B if Minus                     | Result is -ve                                                                    |
| BCC or BLO  | B if Carry clear or B if Lower | Arithmetic operation did not give carry-out or Unsigned comparison gave a lower  |
| BCS  or BHS | B if Carry is Set or same      | Arithmetic operation gave a carry out or Unsigned comparison gave higher or same |
| BVC         | Overflow Clear                 | Signed integer operation gave no overflow                                        |
| BVS         | Overflow set                   | Signed operation gave an overflow into signed bit                                |
| BGT         | Greater Than                   | Signed integer comparison gave >                                                 |
| BGE         | Greater or equal               | Signed integer comparison gave greater or equal                                  |
| BLT         | Less Than                      | Signed integer operation gave <                                                  |
| BLE         | Less than or equal             | Signed integer operation gave <=                                                 |
| BHI         | Higher                         | Unsigned comparison gave >                                                       |
| BLS         | Lower or Same                  | Unsigned comparison gave <=


An unusual feature of ARM instruction set is that conditional execution applies not only to branches but to all ARM instructions. A branch which is used to skip a small number of following instructions may be omitted by giving those instructions the opposite condition as shown below

```
		CMP r0, #5
		BEQ BYPASS
		ADD r1,r1,r0
		SUB r1,r1,r2
BYPASS  
```
can be replaced with
```
		CMP r0, #5
		ADDNE r1,r1,r0
		SUBNE r1,r1,r2
```
This new program is both smaller and faster than the previous. 
>[!note] When the branch is used for 3 instructions o fewer, it is better to use conditional execution

Conditional execution is invoked by adding 2-letter condition after the 3-letter opcode of the instructions. This is applicable to all instructions, the ones dealing with traps and system calls as well.

### Branch and Link instructions
Up and until now the instructions had no capability to return the execution sequence to the line just after the Branch statement after a subroutine or branch specific instructions are executed.
We then use the *branch and link* instruction
#BL 

```
		BL SUBR         ; Branch to SUBR
						; return here
SUBR    MOV PC, r14     ; subroutine empty point
						; return
```

### Supervisor calls
This are routines called to interact with I/O. Supervisor routines operate at a privileged level.  These are used to interact with the hardware directly, using the instruction #SWI

SWI = Software Interrupt, but also works as supervisor call.
There are many and the most common are

1. To transfer 8 bytes to display unit: 
```
SWI SWI_WriteC    ; output r0[7:0]
```
2. To transfer control from user program to monitor program:
```
SWI SWI Exit      ; return to monitor
```

### Jump Tables
#jumpTable 
Advanced feature
The idea is similar to C switch(){} statement.
```
			BL JUMPTAB

JUMPTAB     CMP r0, #0
			BEQ SUB0 
			CMP r0, #1 
			BEQ SUB1
			CMP r0,#2
			BEQ SUB2
```
#DCD is yet to be summarised here from [[Arm System-On-Chip Architecture.pdf#page=79]]


Next → [[Writing Simple Assembly Language Programs]]
