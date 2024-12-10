As a result of CPU scheduling, we can improve both the utilization of the CPU and the speed of the computer's response to its users. To realize this increase in performance, however, we must keep several processes in memory; we must share memory.
In this chapter, we discuss various ways to manage memory.

Memory is a large array of words or bytes, each with its own address. The CPU fetches instructions from memory according to the value of the program counter. These instructions may cause additional loading from and storing to specific memory addresses.

The memory unit sees only a stream of memory addresses; it does not know how they are generated (the instruction counter, indexing, indirection, literal addresses, and so on) or what they are for (instructions or data). We are interested in only the sequence of memory addresses generated by the running program.

## Address Binding
#addressBinding
Usually, a program resides on a disk as a binary executable file. The program must be brought into memory and placed within a process for it to be executed. The collection of processes on the disk that are waiting to be brought into memory for execution forms the #inputQueue.
Most systems allow a user process to reside in any part of the physical memory. Thus, although the address space of the computer starts at 00000, the first address of the user process does not need to be 00000.
Addresses in the source program are generally symbolic (such as COUNT). A compiler will typically #bind these symbolic addresses to relocatable addresses (such as "14 bytes from the beginning of this module"). ==The linkage editor or loader will in turn bind these relocatable addresses to absolute addresses== (such as 74014). Each binding is a mapping from one address space to another.

**The binding of addresses can done at any step of process along the way**:
![[Pasted image 20231214183225.png]]
1. **Compile time** : If it is known at compile time where the process will reside in memory, then absolute code can be generated. For example, if it is known a priori that a user process resides starting at location $R$, then the generated compiler code will start at that location and extend up from there. If, at some later time, the starting location changes, then it will be necessary to recompile this code. The MS-DOS .COM-format programs are absolute code bound at compile time.
2. **Load time** : If it is not known at compile time where the process will reside in memory, then the compiler must generate relocatable code. In this case, final binding is delayed until load time. If the starting address changes, we need only to reload the user code to incorporate this changed value.
3. **Execution time**: If the process can be moved during its execution from one memory segment to another, then binding must be delayed until run rime. Special hardware must be available for this scheme to work

We will look into the implementation of each of these bindings.

## Dynamic Loading
In our discussion so far, the entire program and data of a process must be in
physical memory for the process to execute. The size of a process is limited to the size of physical memory. #dynamicLoading is used obtain better memory-space utilization. **A routine is not loaded until it is called**. **All routines are kept on disk in a relocatable load format**. The main program is loaded into memory and is executed. When a routine needs to call another routine, the calling routine first checks to see whether the other routine has been loaded. If it has not been, the **relocatable linking loader** is called to load the desired routine into memory and to update the program's address tables to reflect this change. Then, control is passed to the newly loaded routine.

The advantage of dynamic loading is that an unused routine is never loaded.

Dynamic loading does not require special support from the operating system. It is the responsibility of the users to design their programs to take advantage of such a scheme. Operating systems may help the programmer, however, by providing library routines to implement dynamic loading.

## Dynamic Linking
Notice that Figure 8.1 also shows dynamically linked libraries. Most operating systems support only static linking, in which system language libraries are treated like any other object module and are combined by the loader into the binary program image of the process.
Rather than loading being postponed until execution time, linking is postponed. This feature is usually used with system libraries, such as language subroutine libraries. 

Without this facility, all programs on a system need to have a copy of their language library included in the executable image. This requirement wastes both disk space and main memory. 

With dynamic linking, a stub is included in the image for each library-routine reference. This stub is a small piece of code that indicates how to locate the appropriate memory-resident library routine, or how to load the library if the routine is not already present. When this stub is executed, it checks to see whether the needed routine is already in memory. If the routine is not in memory, the program loads it into memory. Either way, the stub replaces itself with the address of the routine, and executes the routine. Thus, the next time that that code segment is reached, the library routine is executed directly, incurring no cost for dynamic linking. Under this scheme, all processes that use a language library execute only one copy of the library code.

Unlike dynamic loading, dynamic linking generally requires some help from the operating system.

## Overlays
To allow for process can be larger than the amount of memory allocated to it, a technique called #overlays is used. The idea of overlays is to keep in memory only those instructions and data that are needed at any given time. When other instructions are needed, they are loaded into space that was occupied previously by instructions that are no longer needed.

Special relocation and linking algorithms are required to implement overlays
As in dynamic loading, overlays do not require any special support from the operating system. They can be implemented completely by the user with simple file structures, reading from the files into memory and then jumping to that memory and executing the newly read instructions. The operating system notices only that there is more I/O than usual.
The programmer, on the other hand, must design and program the overlay structure properly. This task can be a major undertaking, requiring complete knowledge of the structure of the program, its code, and its data structures. Because the program is, by definition, large (small programs do not need to be overlayed)

Next → [[Logical vs Physical Address Space]]
