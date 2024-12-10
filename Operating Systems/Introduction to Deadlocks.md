#deadlock
Computer systems have many resources that can only be used by one process at a time. Eg. Printers, keyboards etc. This exclusivity ensures #mutualExclusion , i.e. no process can interfere with the operation of any other process. 
Hence, all operating systems have the ability to (temporarily) grant a process exclusive access to certain resources, both hardware and software.
For many applications, a process needs exclusive access to not one resource, but several. When two or more processes can't finish execution without using a resource that is reserved by the other process, the situation is called a **deadlock.** This can happen both in hardware, as in demanding access to printers at the same time, or in software as in two processes locking records simultaneously.

### Resources
Deadlocks can occur when processes have been granted exclusive access to
devices, files, and so forth, aka **resources**. #fungibleResources are the resources whose duplicates are present in the system. 

Resources come in two types: #preemptable and #non-preemptable. A **preemptable resource** is one that can be taken away from the process owning it without effects. Eg. Memory. 
A **nonpreemptable** **resource**, in contrast, is one that cannot be taken away from its current owner without causing the computation to fail. Eg. CD recorder. 

Preemptive resources generally do not end up in deadlocks due to shifting of instructions. So we will look at non-preemptive resources only.

If the resource is not available when it is requested, the requesting process is forced to wait. In some operating systems, the process is automatically blocked when a resource request fails, and awakened when it becomes available. In other systems, the request fails with an error code, and it is up to the calling process to wait a little while and try again.

### Principles of Deadlock

Deadlock can be defined formally as follows:

>*A set of processes is deadlocked if each process in the set is waiting for an
>event that only another process in the set can cause.*

We assume the system has no interrupts that can release this deadlock, or any threading. We continue in the next page.

Next → [[Conditions for deadlock]]


