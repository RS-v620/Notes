Following represents the microinstruction format for the control memory.
![[Pasted image 20231126205020.png]]
The 20 bits of the microinstruction are divided into four functional parts. F1, F2 and F3 specify microoperations for the computer. The CD field selects the status bit conditions. The BR field specifies the type of branch to be used and finally the AD field stores the branch address. The field is  7 bits wide giving a total of $2^7=128$ addresses.
The microoperations are divided into three fields of three bits each. These bits are encoded to specify seven distinct microoperations. This in total gives us 21 microoperations for a microinstruction to choose from. So 3 microoperations can be chosen for each microinstruction. A 000 in any F field indicates no-op. Furthermore, no conflicting microoperations can be used in a single microinstruction. Example being , clearing the AC and subtracting DR from AC, since both use the AC and are executed at the same time.
Each microoperation so designated is defined by a #RTL statement and is assigned a symbol. Use the following table as a reference only, no need to learn anything here.
![[Pasted image 20231126205928.png]]
In this standard we use max of 5 letters to define a microoperation.

The CD (condition) field consists of two fields which are encoded to specify four status bit conditions as in the table above. 
The BR field consists of two bits which are used in conjunction with the conditional fields to choose the address of next microinstruction. 

### Symbolic Microinstruction
Each line of an assembly language microprogram defines a symbolic microinstruction. Each is divided into 5 fields:
* Label : May be empty or it may specify a symbolic address. Terminated with a COLON
* Microoperation: Contains 1,2 or 3 symbols separated by commas, as in the table above, and only 1 symbol can occupy the F field. NOP is used when no operation is performed. This gets translated to all 0s by the aassembler.
* CD: Has any of the letter U, I, S or Z
* BR: Contains one of the four symbols from JMP, CALL, RET, MAP
* AD: Specifies the value of the address field in three possible ways,
	1. With a symbolic address, which appears as a label
	2. With NEXT to designate the net address in sequence
	3. When BR has RET or MAP, field is left empty and is converted to all 0s by the assembler.
* ORG is used to define the origin of a microprogram routine.

### Fetch Routine
The control memory has 128 words, and each word has 20 bits. The first 64 words are to be occupied by the routines for the 16 instructions. Remaining are free to be used. For fetch, the microinstructions needed are : $$\begin{multline}\\
AR ← PC \\
DR ← M[AR], \;\; PC ← PC+1 \\  
AR ← DR(0-10), \;\; CAR(2-5) ← DR(11-14),\;\; CAR(0,1,6) ← 0 
\end{multline}$$
The address of the instruction is transferred from $PC$ to $AR$ and the instruction is then read from memory into the $DR$. Since no instruction register is available, the instruction code remains in $DR$. The address part is transferred to $AR$ and then control is transferred to one of the 16 routines by mapping the operation code part of the instruction from $DR$ into $CAR$. 
The assembly version of the program is as follows:
```
		ORG 64
FETCH : PCTAR        U   JMP   NEXT
		READ, INCPC  U   JMP   NEXT
		DRTAR        U   MAP
```
Which generates the following binary code:
```
Binary Address    F1    F2    F3    CD    BR    AD
_______________________________________________________
1000000          110   000   000    00    00    1000001
1000001          000   100   101    00    00    1000010
1000010          101   000   000    00    11    0000000
_______________________________________________________
```
Other examples are :

![[Pasted image 20231127165021.png]]


>[!info] I am not sure about the SOFTWARE AIDS segment in the syllabus, I have asked ma'am for help there. Here onwards I assume this unit is complete

Next → [[Home]]




