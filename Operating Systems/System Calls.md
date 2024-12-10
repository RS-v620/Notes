System calls provide the interface between a process and the operating system. System calls occur in different ways, depending on the computer in use. Often, more information is required than simply the identity of the desired system call. The exact type and amount of information vary according to the particular operating system and call.
Three general methods are used to pass parameters to the operating system. The simplest approach is to pass the parameters in **registers.** In some cases,
there may be more parameters than registers, then parameters are stored in a block or table in memory, and the address of the block is passed as a parameter in a register (Figure 3.1). Parameters also can be placed, or pushed, onto the stack by the program, and popped off the stack by the operating system.

System calls can be grouped into five categories:
1. Process and Job Control
2. File Manipulation
3. Device Manipulation
4. Information Manipulation
5. Communications

The details of these are mentioned in subsequent chapters, hence are skipped here.

→----------E--N--D----O--F----------U--N--I--T----------← 

Next → [[Introduction to Processes - Concept of Process]]
