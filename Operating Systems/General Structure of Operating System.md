 We have observed the outline of OS and their types from the programmer's perspective. Now we will see the internal structure classes of OS.

Five designs are there : 
#monolithicSystems #layeredSystems #virtualMachines #exokernels #client-serverSystems
But first we must note the various general components of an OS :
1. **Process Management** : A process needs CPU time, and other resources. These can be given to the process upon execution or allocated. 
	The OS is responsible for :
	* Creation and deletion of user and system processes
	* Suspension/ Resumption of processes
	* Process synchronization
	* Process communication
	* Deadlock handling
>[!info] A process is a program in execution, a program is a passive entity stored in memory
2. **Main Memory Management** : To improve the CPU utilization and computer speed, we need to keep many programs in main memory.  The OS is responsible for : 
	* Keeping track of memory status, used/unused.
	* Deciding the processes that need to be loaded into the memory.
	* Allocate and deallocate memory space.
3. **File Management** : The OS maps files onto physical media and accesses these files via storage devices. 
	*  Creation and deletion of files
	*  Creation and deletion of directories
	*  Supporting manipulatiopns of file and directories
	*  Mapping of files onto secondary storage
	*  Backing up files in non-volatile media
4. **I/O System Management** : This sub-OS hides the I/O details and has :
	* Memory management component  that includes buffers, caches and spooling.
	* Device-driver interface
	* Drivers for specific hardware devices
	The details of the device handling are only privy to the device driver.
5. **Secondary Storage Management** : This sub-OS manages the external/secondary disk drives via
	* Free-space management
	* Storage allocation
	* Disk Scheduling
6. **Networking** : This sub-OS is used in #distributedSystems to control routing, share resources, contention and security. This collect physically separate  and mostly heterogeneous systems into a single coherent system.
7. **Protection System** : Avoids users accessing each other's data. This refers to the mechanism for controlling the access of programs, processes or users to the resources defined by the computer system. Improves reliability by detecting latent errors at interface level
8. **Command Interpreter System** : It is the interface between the user and the operating system. When a batch process starts, or a new user logs-in in a #time-sharingSystems, a program that reads and interprets the control statements is executed, called *command line interpreter or  #shell*** . It has a simple function, get the next command and execute it. These interpreters handle command statements that deal with all the other OS systems.

# Monolithic Systems
#trapInstructions #kernelCall #supervisorCall
These are the most common architecture in the sense that it has no architecture.  The OS is written as a vast set of functions that each have access to each other. Each procedure(function) has a defined list of parameters and are completely free to call all other functions.
This provides limited security.
To construct a program of OS when this is used, the machine compiles the entire function set and then binds them together in a single [[object file]] using a [[Linker]]. 
It is possible to attain a hint of organisation by separating **User Calls** from a *trap* instruction known as **Kernel Call or Supervisor Call**. 
Most CPUs have two modes of operation : *kernel mode* for the OS and *user mode*. In kernel mode, all CPU operations are allowed whereas in user mode instructions are restricted to a subset of the entire instruction set. Mostly I/O instructions and certain critical instructions are not allowed.
The user is allowed to issue parameters in well-defined places such as registers or on the stack. Then the **kernel call** is initiated and the machine transitions to the *kernel mode* transferring all the controls to the OS, which then issues the *System Calls*.
At last we can say that we can classify the function in two categories, the *Service Procedures* and the *Utility Procedures*. Service procedures accompany each defined system call like read, write, jump. Utility procedures are commonly used helper functions such as read from I/P.

![[Pasted image 20231006195255.png]]


# Layered Systems
this approach is a generalization of the monolithic system. We are going to take the example of the TH E system which was first built in Netherlands. This system had six layers with each layer having its own operation.

![[Pasted image 20231006214149.png]]
## Layer 0 : Process Allocation and multiprogramming
#multiprogramming #jobScheduling #triggerHandling 
Level zero is the most fundamental layer and deals with allocation of the processor, switching between processes when interrupts occurred or timers expired.
## Layer 1 : Memory and Drum Management
#memoryManagement #drum #drumManagement
Layer 1 does memory management. It allocates space for processes in main memory and on a word drum used for holding parts of processes for which is no room in memory( #pages). This layer ensures that processes do not have to worry about whether they are in memory or on the drum. The layer-1 software takes care of making sure pages are brought into memory whenever they are needed.
## Layer 2 : Operator - Process Communication
This layer handles communication between each process and the operator console.
>[!info] Operator console just means user I/O devices, includes mice, keyboards, screen
## Layer 3 : I/O Management
This layer takes care of managing the I/O devices and buffering the information streams to and from them. This layer ensures that each process above this layer can deal with  I/O devices without going into their details.
## Layer 4 : User Programs
This is the layer where user programs are found for. Users do not have to worry about process, memory, console, or I/O management.
## Layer 5 : The Operator
#systemOperators
This layer provides system operator processes. System operators are the subprograms in an OS that monitor the entire operation of the machine.

Another generalisation is present in MULTICS where the OS is organised as concentric rings with each inner layer being more privileged than the outer ones. An outer ring needs to send #trapInstructions to inner layers and specific parameters that are pre-tested or consistency to use their services.

# Virtual Machines
#time-sharingSystems 
This OS provides for #multiprogramming, more convenient interface. The heart of the system is #virtualMachineMonitor which runs on the bare hardware and does the multiprogramming, and provides several #virtualMachines to the next layer.
>[!important] These machines are exact copies of the bare hardware


![[Pasted image 20231006221929.png]]
>[!info] CMS means Conversational Monitor System
>This is a #interactive #time-sharingSystems for a single user. Counterintuitive I know



These machines do not provide fancy features, they are just exact copies of the hardware. Because each virtual machine is identical to the hardware, they can run any OS that can run on the hardware. This allows for multiple OS on a single machine.

An OS will generate a system call and uses a #trapInstructions to access its CMS system. This CMS system then issues a hardware instruction, just like it would do to a real hardware. These instructions are trapped by Virtual Machine (370 here refers to the first VM introduced, its name was VM/370), and via #multiprogramming methods executed on the real hardware.
This creates a separation of hardware from the user OS, and provides more flexibility and ease of maintenance.

This is extensively used by web-hosting companies that run multiple virtual machines on a single processor/server that host multiple websites simultaneously. This also finds use in **Java Virtual Machines** which interprets java code. Any computer in the world that has a JVM interpreter can run the program because virtual machines ensure that hardware interface is independent of the program contents. This of-course requires that JVM interpreter be written and installed for that particular hardware.

# Exokernels
This is a variation in the Virtual Machine approach. This approach provides a subset of resources to each user. 
At the bottom layer, running in *kernel mode* is **Exokernel**. Its job is to allocate resources and ensure exclusivity in the use of resources.
This reduces the overhead of mapping resources to their actual location, as was required in Virtual machines, as each user will only have predetermined resources that can be accessed directly.
This process also has the advantage of separating #multiprogramming and user OS code since all it has to do is ensure that no pre-allocated resource is being shared.

# Client-Server Model
Virtual machines made user side OS much simpler since they separated hardware level OS implementation from higher layer OS. But VM is still a very complex program. 
This approach further separates the user side to higher layers and removes as much as possible from the OS, leaving a minimum kernel. The usual approach is to implement most of the OS functions in user processes. The user process ( renamed as #clientProcess) send a request to the #serverProcess which then executes it and sends back the results.

![[Pasted image 20231006225301.png]]
The job of the kernel is to handle communication between clients and servers. OS is split into parts that control only a single aspect of the system, and hence becomes manageable. Also since all servers ( functions) run in *user mode*, they do not have direct access to the hardware. This ensures that a program bug doesn't crash the entire system. The kernel still has the critical task of making the user mode processes interact with the hardware. This makes the Server processes as #drivers.
This system also finds use in #distributedSystems 

Next → [[OS Services]]

