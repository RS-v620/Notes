
The objective of multiprogramming is to have some process running all the time. The job of time sharing is to have the CPU accessible to multiple users. In a uni-processor system, only one process can run at a time

### Scheduling Queues
Processes entering the system are put into ***job queues***, #jobQueue. The processes that are waiting in processor memory waiting to be executed make up the ***ready queue*** #readyQueue. Stored as a linked list. Each #PCB in the list has a link pointing to the next PCB, and the list header has links to the first as well as the last PCB.

When a process has to wait for the response of an I/O device because it is busy, it forms the part of a list termed as ***device queue*** #deviceQueue. Each device has its own device queue. 

![[Pasted image 20231010171620.png]]

Commonly process scheduling is discussed using a *queueing diagram* #queueingDiagram where rectangular boxes represent a queue. 
* Circles represent the resources that serve the queue
* Arrows indicate flow of processes in the system.

A new  process is initially put in the ready-queue, and then is given to CPU, thereafter :
1. The processor can issue an I/O request, and then place the process in I/O queue
2. The process can create a new sub-process and wait for its termination
3. The process could be removed from the CPU due to an interrupt and be put back in ready queue.
![[Pasted image 20231010173124.png]]



### Schedulers
#scheduler 
A process will migrate between many scheduling queues throughout its lifetime and the OS must select from these queues in some way. This selection process is carried out by ***scheduler***.  
In a batch system : 
1. The **long-term schedule ( job scheduler)** #longTermScheduler selects process from the pool of stored processes and loads them into processor memory for execution. **Executes slowly.** It controls the **degree of multiprogramming** i.e. no, of processes in memory. It is important that long-term schedulers select a good mix of *I/O bound processes* and *CPU bound processes*. Binding here means requiring the most time the process will require from. Its job is to issue a new process when a process finishes in processor.
2. The ***short-term scheduler*** or **CPU Scheduler** selects from the processes in memory and allocates CPU to one of them. It is very fast because instructions execute fast.
Hence these work in tandem to do scheduling.

A CPU can continue processing the processes while partially processed processes wait in queues.

Additionally, On #time-sharingSystems , an additional intermediate level of scheduling is often introduced, The **medium level scheduler** removes processes form the memory, thereby reducing degree of multiprogramming, and then later reintroducing the process into the memory with restoring its #PCB. This is called #swapping. This is necessary to improve process mix, to allow equal access to all users, and also deal with memory change requirements where the system has overcommitted available memory.

![[Pasted image 20231010173225.png]]

### Context Switch
#contextSwitch
Switching the CPU to another process requires saving the state of the old process and loading the saved state from the new process. This is known as **context switch**. It causes overhead time waste. 
In different systems context switches are implemented differently. Some require copying of registers, while some require just changing a pointer.
This is avoided at all costs in modern systems using #threads

Next → [[Operations on Processes]] 