It is imperative to note that all the requirement of memory management arises because the main processor memory is limited.

The simplest possible memory management scheme is to run just one program at a time, sharing the memory between that program and the operating system.
Three variations on this theme are shown in Fig. 4-1. The operating system may be at the bottom of memory in RAM (Random Access Memory), as shown in Fig. 4-1(a), or it may be in ROM (Read-Only Memory) at the top of memory, as shown in Fig. 4-1(b), or the device drivers may be at the top of memory in a ROM and the rest of the system in RAM down below, as shown in Fig. 4-1(c).
![[Pasted image 20231214191635.png]]
This allows for only one process at a time to be running. When a new command/program request is sent by the user, the new program is loaded into memory, replacing the older one.

## Multiprogramming with Fixed Partitions
The easiest way to achieve #multiprogramming is simply to divide memory up into n (possibly unequal) partitions. This partitioning can, for example, be done manually when the system is started up.
When a job arrives, it can be put into the #inputQueue for the smallest partition large enough to hold it. Since the partitions are fixed in this scheme, any space in a partition not used by a job is wasted while that job runs. In Fig. 4-2(a) we see how this system of fixed partitions and separate input queues looks.
![[Pasted image 20231214192329.png]]
The disadvantage of sorting the incoming jobs into separate queues becomes apparent when the queue for a large partition is empty but the queue for a small partition is full, as is the case for partitions 1 and 3 in Fig. 4-2(a).
An alternative organization is to maintain a single queue as in Fig. 4-2(b). Whenever a partition becomes free, the job closest to the front of the queue that fits in it could be loaded into the empty partition and run. Since it is undesirable to waste a large partition on a small job, a different strategy is to search the whole input queue whenever a partition becomes free and pick the largest job that fits.

## Relocation and Protection
Multiprogramming introduces two essential problems that must be solved— relocation and protection.
As in figure-4.2, the end location of the process is not decided until it is stored in memory, which renders the absolute binding and linker bindings useless. Also there is the problem of a malicious program jumping to the address allocated to some other process. 
A solution is to include a **base** and **limit** register. #baseLimitRegister. When a process is scheduled, the base register is loaded with the address of the start of its partition, and the limit register is loaded with the length of
the partition. Every memory address generated automatically has the base register contents added to it before being sent to memory. Thus if the base register contains the value 100K, a CALL 100 instruction is effectively turned into a CALL 100K + 100 instruction, without the instruction itself being modified. 

Addresses are checked with the limit register to forbid any out of range memory access. The **base** and **limit** registers are protected from modifications by the hardware. 

Next → [[Swapping]]



