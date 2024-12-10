#Implementation
# Binary Adder
The digital circuit that generates the arithmetic sum of two binary numbers of any length is called a binary adder. It is constructed using full adder circuits in cascade.
![[Pasted image 20230911140226.png]]
Here $C0$ is generally the system carry flag, and the output of $C4$ goes into the flag as well. The sum $S$ can be stored in either a separate register or in one of the operand registers.

# Binary Adder-Subtractor
As noted before, subtraction is equivalent to adding 2's complement of a binary number to the other. The 2's complement is achieved by taking 1's complement (using NOT gates) followed by addition of 1. 
Addition and subtraction can be combined in a common circuit by including an Exclusive-OR gate with each full-adder. 
![[Pasted image 20230911141823.png]]
An additional input named $M$ (mode) controls the operation: 

$$Given \;Registers \;R_{1}\; \& \;R_{2}\;\; S = {\begin{cases}
R_{1}+R_{2} &,\; M=0\\
R_{1}-R_{2} &, \;M=1\\

\end{cases}}
$$
## Working

When $M=0$, the XOR gates do not affect the information bits provided by B, as $B\; \oplus \; 0 = B$, and the input carry itself is 0. So the circuit performs as an adder. 
When $M=1$, the XOR gate performs as a NOT gate since, $B\oplus1=\overline{B}$. Further, the input carry $C0$ is now 1, which now adds the necessary 1 for 2's complement.
**In unsigned numbers this gives $A-B$ if $A \underline{>} B$  or the 2's complement of $(B-A)\; if\;A<B$ provided there is no overflow.

# Binary Incrementor
Adds 1 to a register. The following diagram showcases a 4 bit incrementor. One of the inputs to the least significant half adder is connected to logic 1. The output carry from one half adder is passed as input to the next half adder. [[Half-Adder and Full-Adder]] are circuits that perform additions. 
The output carry $C4$ will be 1 only in the case of $A = 1111$ which result in output $S_{0}$ through $S_{3}$ to go to 0. The circuit can be implemented for $n$ bit incrementor by adding more Half-Adders.
![[Pasted image 20230911223521.png]]


# Arithmetic Circuit

^b47444
^8f5363
[[Microoperations#^4842bf]] *table of arithmetic operations*

All arithmetic operations can be implemented using a single circuit. It has 4 full-adder circuits that constitute the 4-bit adder and four multiplexers for choosing different operations. Inputs are $A$ and $B$ , and output is $D$, all 4 bit.
![[Pasted image 20230911224637.png]]
Inputs from $A$ go directly to the adders. Bits of $B$, $B_{i}$ and their complements $\overline{B_{i}}$ are provided to the multiplexers. The other two data bits of MUX are connected to logic-0 and logic-1. $S0$ and $S1$ act as select bits for operations. 

The output from the circuit is : $$ D = A+Y+C_{in}$$
The $+$ here denotes the arithmetic plus and not the binary operation $OR$ . 

---

| Select | <     | <      | Input     | Output                                |                   |
| ------ | ----- | ------ | --------- | ------------------------------------- | -------------------- |
| S_{1}  | S_{0} | C_{in} |  Y |  D=A+Y+C_{in}                  |    Microoperation       |
| 0      | 0     | 0      | B         | D=A+B                                 | Add                  |
| 0      | 0     | 1      | B         | D = A+B+1                             | Add with Carry       |
| 0      | 1     | 0      | B'        | D = A+B'                              | Subtract with borrow |
| 0      | 1     | 1      | B'        | D = A + B'+1                          | Subtract             |
| 1      | 0     | 0      | 0         | D = A                                 | Transfer A           |
| 1      | 0     | 1      | 0         | D = A+1                               | Increment A          |
| 1      | 1     | 0      | 1         | D = A-1* (Overflow reduces count by 1) | Decrement A          |
| 1      | 1     | 1      | 1         | D = A                                 | Transfer A           |

>[!note] When $S_{0},\; and \; S_{1}$ are 1, Input-3 is selected from the MUX. It adds, in this case $1111_{2}$ to the number A. One count is consumed in resetting all the adders to $0000_{2}$  and the rest $A-1$ count make the final ouput, giving a net expression of a decrement $A-1$.
# Logic Circuit

^f6c27d

![[Pasted image 20230911233120.png]]

This circuit makes up all the necessary logic microoperations a computer might require as a combination of select lines and inputs. Self explanatory.

# Arithmetic Shift

^950543

Realised using a bi-directional shift register with parallel load. Information is transferred in parallel, and then rotations are handled serially. 
![[Pasted image 20230913212824.png]]

If $S=0$, the selected bits are $$H_{3} H_{2} H_{1} H_{0} = A_{2} A_{1} A_{0} I_{R}$$
and if $S=1$, the selected bits are: $$H_{3} H_{2} H_{1} H_{0} = I_{L} A_{3} A_{2} A_{1}$$
Here it is important to note that the convention considers the data to be structured as $0th\;bit, 1st\;bit\;...$ and so on. So a right shift means removal of $A_{3}$, and a left-shift mean removal of $A_{0}$.

# Arithmetic-Logic Shift Unit
This is an overall circuit that is used to perform all operations. It combines the arithmetic circuit and logic circuit to make a single circuit as below.
![[Pasted image 20230913213749.png]]

With the operations being as following, depending on the values of Select lines.

![[Pasted image 20230913213835.png]]

In this circuit, the shift operation circuit is not drawn, just its output lines are shown.

