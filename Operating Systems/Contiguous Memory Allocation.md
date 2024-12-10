The main memory must accommodate both the operating system and the various user processes. 

## Single Partition Allocation
If the operating system is residing in low memory and the user processes are executing in high memory, we need to protect the operating-system code and data from changes (accidental or malicious) by the user processes. We also need to protect the user process fro one another. The #MMU maps the logical address *dynamically* by adding the value in the #reocationRegister. When the CPU scheduler selects a process for execution, the dispatcher loads the relocation and limit  registers with the correct values as part of the context switch.

Device drivers are not always needed for execution and may be swapped and out of the memory as needed, giving the OS a variable size during program execution.

## Multiple-Partition Allocation
To allow for multiprogramming, multiple user processes reside in memory. One of the simplest schemes for memory allocation is to divide memory into a number of fixed-sized partitions. 
The operating system keeps a table indicating which parts of memory are available and which are occupied.
Initially, all memory is available for user processes, and is considered as one large block of available memory, a #hole. 
When a process arrives and needs memory, we search for a hole large enough for this process. If we find one, we allocate only as much memory as is needed, keeping the rest available to satisfy future requests.

In general, there is at any time a set of holes, of various sizes, scattered throughout memory. When a process arrives and needs memory, we search this set for a hole that is large enough for this process. If the hole is too large, it is split into two: One part is allocated to the arriving process; the other is returned to the set of holes. When a process terminates, it releases its block of memory, which is then placed back in the set of holes. If the new hole is adjacent to other holes, we merge these adjacent holes to form one larger hole
![[Pasted image 20231214200839.png]]
This procedure is a particular instance of the general #dynamicStorageAllocationProblem, which is how to satisfy a request of size n from a list of free holes. Three solutions:
1. **First-fit**: Allocate the first hole that is big enough.
2. **Best-fit**: Allocate the smallest hole that is big enough. We must search the entire list, unless the list is kept ordered by size. This strategy produces the smallest leftover hole.
3. **Worst-fit**: Allocate the largest hole. Again, we must search the entire list, unless it is sorted by size.

## External and Internal Fragmentation
The algorithms described in Section 8.4.2 suffer from #externaFfragmentation. As processes are loaded and removed from memory, the free memory space is broken into little pieces. External fragmentation exists when enough total memory space exists to satisfy a request, but it is not contiguous; storage is fragmented into a large number of small holes.
Even with some optimization, given N allocated blocks, another 0.5N blocks will be lost due to fragmentation That is, one-third of memory may be unusable! This property is known as the #50-percentRule. 

Another problem that arises with the multiple partition allocation scheme. Suppose that the next process requests 18,462 bytes. If we allocate exactly the requested block we are left with a hole of 2 bytes. The overhead to keep track of this hole will be substantially larger than the hole itself. The general approach is to allocate very small holes as part of the larger request. Thus the allocated memory will be slightly larger than the requested memory. The difference between these two numbers is #internalFragmentation—memory that is internal to a partition, but is not being used. 
One solution to the problem of external fragmentation is compaction. The goal is to shuffle the memory contents to place all free memory together in one large block.
![[Pasted image 20231214201455.png]]
Compaction is possible only if relocation is dynamic, and is done at execution time. If addresses are relocated dynamically, relocation requires only moving the program and data, and then changing the base register to reflect the new base address.
Selecting an optimal compaction strategy is quite difficult. 
Optimal solutions require dynamic allocation of space while swapping in co-ordination with compaction.

Next → 