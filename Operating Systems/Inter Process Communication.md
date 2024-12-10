#IPC #interProcessCommunication
Processes frequently need to communicate with other processes. 
Three things to look out for :
1. How to pass information
2. How to ensure processes do not use each other's resources
3. Proper sequencing of processes to ensure that if output of process B depends on output of process A, A is executed before B.

### Race conditions
#raceConditions
Processes, especially when sharing resources like memory or some form of common storage media might end up overwriting each other's work. As an example if process A stores information at location F00012 and before it can do something, before the *free_slot_register* ( a register that stores the address of next empty memory cell) updates with the new empty address an interrupt comes and uses the value in *free_slot_register* to store its data at F00012 as well. This is called **race condition**.
### Critical Sections
#mutualExclusion #criticalRegion #criticalSection
The race condition can be prevented with **mutual exclusion**, preventing processes from accessing resources currently in use. 
The part of the program where the shared memory is accessible is called the **critical region**/**critical section**. 
The desired behavior is as shown below : 

![[Pasted image 20231010190813.png]]
Process $B$ will be blocked from using the critical section until process $A$ has completed its use.  This uphold 4 rules :
1. No two processes must be using the critical region at the same time
2. No assumption must be made about the usage requirements of the other processes
3. No process can reserve the critical region for itself
4. No process should have to wait forever to access the critical region, i.e. processes have a maximum time limit for which they can access the critical regions.

### Mutual Exclusion with Busy Waiting
#mutualExclusion #busyWaiting
Various methods for mutual exclusion are discussed 
#### Disabling Interrupts
The problem arose due to interrupts causing memory override, but it would be an unwise decision to block interrupts in hardware because it is dangerous giving processes control of interrupts, instead the kernel can handle interrupt disabling for a few instructions while it handles updating of lists and variables. Overall, it is not an appropriate solution.
#### Lock  variables
This method instates a bit that has the job of guarding memory space. During use, the bit is set and no further updations can be made unless a process completes and resets it. 
This approach also has the fatal flaw that before a process could set the bit, the interrupt process can access the bit and set it to 1. Later the original process also sets it to 1 and enters the critical region, defeating the purpose
#### Strict Altercation
#busyWaiting
This method works by letting the guard bit decide which process can enter the critical region. Say for process $A$ and $B$, $A$ is allowed when the guard bit is 0 and $B$ is allowed when it is one. It avoids all forms of race conditions. Each process flips the guard bit while finishing execution.

Each process waits until the guard bit becomes suitable to them, this is called **busy waiting**.
But it also causes a slow/lengthy program to reserve the critical region while it is not using it. Which violates the non-reservation rule.

Consider that $A$ runs when guard bit is 0, performs its tasks in the critical region, and then sets the bit to '1' while it performs the non-critical region tasks. Process $B$ is fast and quickly uses the critical region tasks and non-critical region tasks and can access the critical region again while $A$ is still in non-critical region, but it can't do so because the $B$ set the guard bit to '0' while exiting and now must wait for $A$ to set it to '1' again. 

#### Peterson's Solution
#petersonsSolution
This guy combined all the ideas, but made a software solution to the problem,
that doesn't require strict altercation. This algorithm is written in ANSI C. It is better to understand with the program
```
#define FALSE 0
#define TRUE  1
#define N     2    //no. of processes

int turn;          // whose turn

int interested[N]; // all values 0 initially

void enter_region( int process) // process is named 0 or 1
{
	int other;     // var for other process

	other = 1 - process; // the opposite of process, the other process
	interested[process] = TRUE; //indicate interest to enter region

	turn = process;  // indicate turn, whose turn it i

	while(turn == process && interested[other] == TRUE); // wait
}

void leave_region( int process)
{
	interested[process] = FALSE; // stop asking for resource allocation
}
```

Each process calls *enter_region* with its own process number, 0 or 1. It causes the second coming process to enter the last line, while loop until the other process declares it interest as FALSE. 
In case of simultaneous calls, suppose the two processes $A$ and $B$ call the functions at the same time, suppose $B$ comes a moment later and sets **turn** to 1. So process $A$ 's while loop will not work while $B$ will be stuck. 

#### The TSL instruction
#TSL 
Many computers have an instruction that works as 
$$TSL\;\;\;RX,\;LOCK$$
(***Test and Lock***), it reads the contents of memory word LOCK and stores a non-0 value there. These are implemented as simultaneous operations, happen at the same time, so no interruptions. **The CPU executing the TSL instruction locks the memory bus** to prohibit other CPUs from accessing memory until it is done. No other processor can access the memory word until the instruction is finished.
Using a generic assembly language format
![[Pasted image 20231010202051.png]]


### Sleep and Wakeup
#sleep #wakeup
Both Peterson's solution and TSL are correct but require #busyWaiting. 

We can consider #priorityInversionProblem, that occurs with the above solutions. A higher priority process $H$ and lower one $L$ share the same resources. While $L$ is executing, the procedures block the critical region and $H$ can't access it. $H$ now begins waiting. But since $H$ has higher priority, $L$ will never be scheduled while $H$ is running and $H$ is stuck in *busy waiting* forever. 

One of the simplest solution is using **sleep** and **wakeup**. These are functions implemented either in the hardware or software.
**Sleep** : A system call that causes the caller to be suspended until another process wakes it up
**Wakeup** : Has one parameter, the process to be awakened.

#### Producer-Consumer Problem
#producer-consumerProblem #boundedBufferProblem 
Suppose in a shared memory resource, one process is the provider of information and the other is a consumer. Additionally, when the memory resource is full, the producer goes to sleep, and if it is empty, the consumer goes to sleep. They also wakeup each other when there is data to be consumed or memory is less than full. This requires that they maintain a count of the used memory spaces.
So a race happens when the access to the *count* variable is unrestrained.
>The buffer is empty and the consumer has just read count to see if it is 0. At that instant, the scheduler decides to stop running the consumer temporarily, put it to sleep, and start running the producer. The producer enters an item in the buffer, increments count, and notices that it is now 1. Reasoning that count was just 0, and thus the consumer must be sleeping, the producer calls wakeup to wake the consumer up. Unfortunately, the consumer is not yet logically asleep, the issuing of wakeup signal happened before or during the time when scheduler asked the consumer to sleep, so the wakeup signal is lost. When the consumer next runs, it will test the value of count it previously read, find it to be 0, and go to sleep. Sooner or later the producer will fill up the buffer and also go to sleep. Both will sleep forever.

The essence is that the ***wakeup* sent to a not yet asleep is lost**. If this doesn't happen everything will work.

A solution is to use a #wakeupBit that gets set whenever a wakeup is ordered. When a process tries to go to sleep, if the wakeup bit is set, it continues to stay awake.
### Semaphores
#semaphores
The problem is exaggerated when we have more than 2 processes, we would need multiple wakeup bits. The further solution is a new type of variable i.e. **semaphore**, which is an integer variable to **count the number of wakeups saved for the future**. Values range from 0-INT_MAX. 
It then uses  two operations, **down** and **up**(which generalizes **sleep** and **wakeup**). 
The **down** operation in a semaphore checks to see if the value is greater than 0. If so, it decrements the value ( i.e. uses up one stored wakeup) and just continues. If the value is 0, the process is put to sleep without completing down for the moment. Checking the value, and possibly going to sleep is done in a single action. 
The **up** operation increments the value of the semaphore addressed. If one or more processes were sleeping on that semaphore, unable to complete and earlier **down** operation, one of them is **chosen by the system** and are allowed to complete their **down** operation.  So after an up operation with a process sleeping on it, the semaphore will be 0, but 1 less process sleeping. 
**up** as a process is never blocked. 

This solves the #lost-wakeupProblem aka #producer-consumerProblem 

Semaphores that are initialized to 1 and used by two or more processes to ensure only one of them accesses the critical region are called #binarySemaphores. If each process performs a **down** before entering the critical region and an **up** after entering it, mutual exclusion is guaranteed.

The other use of semaphores if for #synchronization . 
In practical applications three semaphores are used : **full** that counts the number of memory slots occupied, **empty** that counts the empty slots, and a **mutex** that is set to 1 initially.

### Message Passing
Semaphores are used to achieve mutual exclusion in a single processor. 
Semaphores with a TSL instruction is used for mutual exclusion in a multi-processor computer system.
But these can't be used to manage a distributed system. Too low level. Noobs.
Here we use #messagePassing. 
It has two methods of communication, **send** and **receive**. These are system calls like semaphores, and are put into library procedures.
#### The producer-consumer problem with message passing
In this solution, we assume that all messages are the same size and that messages sent but not yet received are buffered automatically by the operating system. In this solution, a total of N messages is used, analogous to the N slots in a shared memory buffer. ==The consumer starts out by sending N empty messages to the producer.== ==Whenever the producer has an item to give to the consumer, it takes an empty message and sends back a full one.== In this way, the total number of messages in the system remains constant in time, so they can be stored in a given amount of memory known in advance.

If the producer works faster than the consumer, all the messages will end up
full, waiting for the consumer; the producer will be blocked, waiting for an empty
to come back. If the consumer works faster, then the reverse happens: all the
messages will be empties waiting for the producer to fill them up; the consumer
will be blocked, waiting for a full message.

##### Mailbox approach
A different way is to invent a new data structure, called a mailbox. A mailbox is a place to buffer a certain number of messages, typically specified when the mailbox is created. When mailboxes are used, the address parameters in the send and receive calls are mailboxes, not processes. When a process tries to send to a mailbox that is full, it is suspended until a message is removed from that mailbox, making room for a new one.
When mailboxes are used, the buffering mechanism is clear: the destination mailbox holds messages that have been sent to the destination process but have not yet been accepted. 

##### Rendezvous approach
The other extreme from having mailboxes is to eliminate all buffering. When this approach is followed, if the **send** is done before the **receive**, the sending process is blocked until the receive happens, at which time the message can be copied directly from the sender to the receiver, with no intermediate buffering. Similarly, if the receive is done first, the receiver is blocked until a send happens. This strategy is often known as a #rendezvous.
It is easier to implement than a buffered message scheme but is less flexible since the sender and receiver are forced to run in lockstep.

Next → [[CPU Scheduling]]
