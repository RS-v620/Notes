#RegisterTransferLanguage #RTL

Digital modules are best defined by the registers they contain and the operations that are performed on the data stored in them.
The operations executed on the data stored in the registers are call **microoperations**.
It is verbose to define each microoperation in words so instead *register transfer language*. It defines a set of notations that represent the various microoperations

---
Rules and Standards in Register Transfer Language

---


1. Register names are designated by capital letters and numerals. A few examples are:
	* MAR - Memory Address Register #MAR
	* PC - Program Counter #PC
	* IR - Instruction Register #IR
	* DR - Data Register #DR
1. Individual bits in a register are represented by encasing the bit number/bit range in a parenthesis.
	* PC(0)
	* PC(0-7)
	* PC(L) - L Indicates the least significant byte in a multi-byte register.
	* PC(H) - H indicates the most significant byte similarly
2. Information transfer is indicated by the use of ← between the names of the registers as following: $$ R1 ← R2 $$
This denotes a transfer of contents from $R2$ to $R1$. The contents of $R2$ are not altered.
4. If a control statement is required before a transfer, it is achieved by using *If-then* statement : $$If (P = 1)\;then\;(R1 ← R2)$$Oftentimes it is conventional to use the control variable as a *control function* that controls this transfer: $$ P:\;R1 ← R2$$
**Note** : Each Register Transfer notation is based on a hardware implementation. And each control signal is synchronized with the same clock that is applied to the registers.

![[Pasted image 20231009220938.png]]

# Bus and Memory Transfer
Buses are necessary because individual wires from register-to-register would require too many wires and consume a lot of space. It is thus efficient to have a bus. A bus structure consists of a set of common lines, one for each bit of the register, through which binary information is transferred one at a time.
The **bus** is traditionally achieved using multiplexers as shown below.

![[Bus System for 4 Registers.png]]
Here it is seen that for each bit of a single register there is a corresponding multiplexer that connects to its bit. Each multiplexer takes in multiple inputs from different registers but the same bit of each register. Select lines $S1$ and $S2$ are used to select the register to read from. The 4 multiplexers are used to transfer 4 bits of $Register\;A, B, C\;or\;D$ depending on the status of select lines. 
This means that a common bus for 4 resisters with 16 bits in each will require 16 multiplexers with 2 select lines shared across them.
The transfer of information in a bus is shown as following: $$ BUS ← C , \; R1 ← BUS$$

This notation can be ignored if Bus is an inherent part of the system and simply be replaced by: $$ R1 ← C $$
## Three-State Bus Buffers
Instead of Multiplexers, we use [[Three State Gates]].
The high impedance state of the thee-state gate provides a large number of three-state gates outputs which can be connected simply with wires to form a common bus line, without having any loading effects in the circuit. 
The three state gate is used to make the Bus as shown below. It is important to note that this type of connection can't be made in the presence of any other gates that don't have three state outputs.
![[Bus Line with Three state Buffers also 4x1 Multiplexer.png]]

It is imperative to note that this is the structure of a **4x1 multiplexer**.

## Memory Transfer
The memory of a system is divided into words. Memory words are symbolised by *M* and the address to which they belong are marked by *AR*. 
1. Read operation : $$Read : \; DR ← M[AR]$$
2. Write operation : $$ R_{3} ← R_{1} + \overline{R2} + 1$$
* Here $\overline{R2}$ is the 1's complement of $R2$.


Next → [[Microoperations]]