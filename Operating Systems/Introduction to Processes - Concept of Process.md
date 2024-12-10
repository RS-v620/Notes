#process
A **process** can be thought of as a program in execution. Resources are allocated to the process either when it is created, or while it is executing.

A process is the unit of work in most systems. Such a system consists of a collection of processes: Operating-system processes execute system code, and user processes execute user code.

The operating system is responsible for the following activities in connection with process management:
1. the creation and deletion of both user and system processes; 
2. The scheduling of processes; 
3. and the provision of mechanisms for synchronization, communication, and deadlock handling for processes.

The execution of process must progress in a sequential fashion. At most one instruction is executed on behalf of the process. 
A process is composed of :
1. Program code aka *text section*
2. Values in the Program Counter #PC 
3. Contents of the Processor's registers
4. Stack
5. Temporary data
6. Data section containing global variables.
i.e all the information that makes up the process.
## Process State
#processState
As a process executed, it changes *state*. We define a state by the current activities of the process. The following are the states a process takes

1. **New** :  A process is being created
2. **Running** : Instructions are being executed
3. **Waiting** : The process is waiting for some event to occur ( I/O completion or a signal)
4. **Ready** : The process is waiting to be assigned to a processor.
5. **Terminated** : The process has finished execution.
![[Pasted image 20231010151954.png]]
>[!note] Only one process can be *running* on any processor at a time


## Process Control Block
#PCB
Each process is represented in the operating system by a process control block. i.e. when all this is happening, the operating system keeps a record like this. This is used to restart the process when interrupt or scheduling causes the process to wait.

![[Pasted image 20231010152651.png]]
 It has data about the processes like : 
 * **Process state**
 * **Program Counter** : Indicating the address of the next instruction
 * **CPU Registers** : Along with the program counter these must be saved when an interrupt occurs, to allow the process to complete. 
 * **CPU Scheduling Information** : This information includes process priority, pointers to scheduling queue, and other scheduling parameters.
 * **Memory-management information** : This can include information about base and limit registers, page tables, segment tables.
 * **Accounting Information** : Includes the CPU and real time used, time limits account numbers or process numbers.
 * **I/O status information** : Information has data about the list of I/O devices allocated to this process, list of open files etc.
The **PCB** serves as the repository for any information that may vary from process to process

Next → [[Process Scheduling]] 