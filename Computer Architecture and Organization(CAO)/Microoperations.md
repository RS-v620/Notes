#microoperation
A *microoperation* is an elementary operation performed on the information stored in registers.
The internal hardware organisation of a digital computer is best defined by specifying: 
	1. The set of registers it contains and their fucntions.
	2. The sequence of microoperations performed on the binary information stored in the registers.
	3. The control that initiates the sequence of microoperations.

Types of microoperations : 
	1. Register Transfer 
	2. Arithmetic
	3. Logical
	4. Shift
	5. Control

# Register Transfer Microoperations
These operations do not change the information content of register and are used to move information from one register to another, or to memory. All other microoperations change the contents of the registers during transfer. Examples are $R1 ← R2$ and so on.
# Arithmetic Microoperations
#arithmeticMicrooperations
^4842bf

| Symbolic Diagram              | Description                                    |
| ----------------------------- | ---------------------------------------------- |
| R3 ← R1 + R2                  | Contents of R1 plus R2 transferred to $R3$ |
| R3 ← R1 - R2                  | Contents of R1 minus R2 going to R3      |
| R2 ← \overline{R2}                      | Complement the contents of R2                |
| R2 ← \overline{R2} + 1                   | 2's complement the contents of R2 (negate)   |
| R1 ← R1 + \overline{R2} + 1               | Equivalent of R3 ← R1 - R2                   |
| R1 ← R1 + 1                   | Increment the contents of R1                 |
| R1 ← R1 - 1                   | Decrement the contents of R1                 |

In most systems multiplication and division is implements as additions/subtractions and bit shifts hence they are not part of the table.
All these arithmetic microoperations can be performed using a single circuit as shown in [[Microoperation Implementation#^8f5363]]

# Logic Microoperations
^LOGIC
#AND #OR #COMPLEMENT
Special symbols are used to denote logical functions to separate them from Boolean functions.
* $\lor$ **:** $OR$ microoperation
* $\land$ **:** $AND$ microoperation
* $\overline{ }$ **:** Complement Operations, placed on top of a register's name
There are 16 different logic operations that can be performed with two binary variable of 1 bit each. Since the two variables can occur in 4 combinations, each combination gives rise to $2^4$ different logic outputs. Most computers only use 4 $(OR$, $XOR$, $AND$, $NOT)$ , which can derive the other relations if necessary. 
![[Pasted image 20231009221130.png]]
>[!important]

Implementation can be seen from [[Microoperation Implementation#^f6c27d]].
## Bit manipulation by logical microoperations
### Selective Set
#selectiveSet
Binary operation. Sets the bit value in first register to 1 wherever it is 1 in second register. 

| 1010 | A before         |
| ---- | ---------------- |
| 1100 | B(logic operand) |
| 1110 | A after          |

Equivalent to : $A ← A\lor B$ 
### Selective Complement
#SelectiveComplement
Complements the bits in A where bit is set in B.
1010 $\oplus$ 1100 → 0110
Equivalent to :  $$A ← A\oplus B$$
### Selective Clear
#selectiveClear
ANDing with complement of Logic operand
$$A ← A\land\overline{B}$$
### Mask 
#mask
ANDing with 0s. Similar to selective clear , but $B$ is used instead of $\overline{B}$.
### Insert
#insert
ORing bits, where important bits have been masked

# Shift Microoperations
^881751
#shiftMicrooperations
Used for serial transfer of data and in conjugation of other microoperations. Bits are shifted bidirectionally and input is fed through a Serial Line. 

![[Pasted image 20231009222649.png]]

## Logical shift
#shl #shr
It transfers 0 through serial input. The symbols for the shifts are :
$R1 ← shl\;R1$
and $R1 ← shr\;R1$
The register symbol must be same on both sides.

## Circular Shift
#cir #cil
Rotates the entire contents of the register. Achieved by connecting the **Serial-OUT** of the register to its **Serial-IN**. No loss of data in the registers

## Arithmetic Shift
#arithmeticShift #ashl #ashr
This microoperation shifts a **signed** binary number to the left or right. This is equivalent to multiplying/dividing the number by 2. These leave the sign bits unchanged (0 for $+ve$ and 1 for $-ve$ ). 
In right shift, least significant bit is lost and in left-shift, most significant bit is lost.
In right-shift, **sign bit** $R_{n-1}$ is preserved, whereas in left-shift, it gets lost and will lead to overflow if $R_{n-1}$ and $R_{n-2}$ have different bits. This will cause the sign of the shifted number to change. This is usually addressed by the use of a flag that perform: $$V_{s} = R_{n-1}\oplus R_{n-2}$$
If $V_{s}=0$, no overflow has occurred.
The implementation of the circuit is available [[Microoperation Implementation#^950543]].

