#OperatingSystems
#Spooling
The operating systems have been evolving over time and the following categories represent the various OS across the ages

# Simple Batch Systems
#batchSystem 
Early computers were large. The input devices consisted of **card readers and tapes drive**. The output devices were **line printers, tape drives and card punches**.There was a separate programmer and computer operator. 
The purpose of operating system was to transfer control form one task to another, and the OS always stayed in memory. 
The operator had to **batch together jobs that were similar**, and then they passed the instruction onto the computer; this batching was done for better efficiency. The outputs of each batch were then sent to the respective programmers. 
A batch OS simply reads a stream of jobs from its input devices, with each job having its own control cards, and then print the output on the line printers. This model doesn't have **Interaction between user and computer while the process is being executed**.

To speed up the processing, *disk readers* were added in the system that can temporarily store the incoming readings from I/P and outgoings for O/P, making the entire process more efficient, since the readers can continue reading while the CPU is executing instructions from a different process, and printers can keep printing. In this methodology, the readers and printers were separate from the CPU. This efficient method is called **spooling**. #spooling

## Spooling
Spooling is an acronym for **simultaneous peripheral operation on-line**. It uses the disk as a huge buffer. It is also used for processing data at remote sites.
## Turnaround Time
Defined as the time it takes to complete a process/job by the computer after submitting a job request. In the systems that didn't use spooling this can be of minutes, hours or days.

# Multiprogrammed Batch Systems

#jobScheduling #multiprogramming #CPUScheduling #jobPool
Spooling provides the computer with a #jobPool waiting to be executed on the hard drive, which is separate from the internal memory of the computer. 
When jobs are stored in hard drives like this, it is possible to reorder the job order, and **job scheduling** becomes possible. This allows for **multiprogramming** which always keeps the CPU busy as long as there are jobs to finish. 
1. **Job Scheduling** : When several jobs are in the job pool waiting to be brought into memory, the OS decides and assigns priority, this is job scheduling.
2. **Multiprogramming** : A CPU takes in multiple jobs at the same time in its local memory. If the current task is waiting for some external input, like insertion of a card or disk, instead of letting the CPU sit idle, the OS asks the CPU to execute the other jobs in the memory. This is the ***first instance in which the OS makes decisions for the user***. 
3. **CPU Scheduling** : When multiple jobs are present in the memory at the same time, the OS has to decide the execution order to increase efficiency. This is CPU Scheduling.
# Time-Sharing Systems

#multitasking #interactive #on-lineFileSystem #directories #time-sharingSystems #virtualMemory

I/P : Keyboards  O/P : CRT Monitors
1. **Difficulties with batch systems** : Since a user can't interact with the job while it is executing, it must set up instruction cards for all possible outcomes including errors and lags. In case of multistep programs this becomes very difficult. Since a programmer can't interfere in between the program execution, a programmer must debug by using snapshot outputs (called dumps). 

2. **Time sharing *or* Multitasking** is built upon multiprocessing batch systems. The CPU regularly switches between processes (*where a process is defined as a program loaded in the memory and being executed*), but in time-sharing systems it switches so fast that **users** can interact with the program while it is running. 
	This *interactive* or *hands-on* computer systems provides live on-line communication between the user and the system. The user gives instruction to the OS or to a program directly and receives an immediate response. The input is provided via a keyboard, and output via a CRT monitor. This allows for quick experimentation and immediate results. 
3. This requires an ***on-line file** system* to access both data and code conveniently. OS maintains these files in tapes and disks and organizes them in clusters called **directories**. It also provides file access control to maintain file access permission tables.
4. This kind of OS causes considerable CPU idle time since user input is required after each job. But on the other side if multiple users are present, this OS provides cheap and interactive use of a CPU to multiple programmers at the same time using **CPU Scheduling and Multiprogramming**.
5. To obtain reasonable response time for each user, jobs may have to be swapped in and out of the main memory, to the disk, which serves as the storage for main memory. This is achieved using a method called **virtual memory**. This allows execution of a program that is not completely in memory, and allows hence programs larger than the CPU memory to be executed. This also abstracts the memory in a large uniform array.
All in all, time-sharing systems use :
1. On-line file system
2. Disk management for interaction between file system and disks
3. CPU scheduling
4. Multiprogramming and finally
5. Virtual memory executions of programs

# Parallel Systems
#gracefulDegradation #multiprocessorSystems #tightlyCoupledSystems #faultTolerant #symmetricMultiprocessing #asymmetricMultiprocessing 
Computers went on to include multiple processors becoming *multiprocessing systems*. These processors communicate with each other extensively and share resources such as buses, clock, memory and peripheral devices. These are hence called *tightly coupled systems*. These improve throughput by delegating the job to multiple processors which can work together to process the job faster. N additional processors do not mean N times the execution speed though because sharing of resources and inter-processor communication creates some bottlenecks. Despite this they offer multiple advantages :
1. Cheaper to run
2. Several programs running on the same data/peripherals/power supplies now share the resources
3. Increased reliability of outcomes and protection against system failures. Failure of one processor doesn't stop the job, just delays it because rest of the processors must now finish its job. This is called *graceful-degradation* of systems and such systems are called *fault tolerant*.

Continued operation in the face of failure requires a mechanism to allow failure to be detected, diagnosed and corrected if possible. There are several ways to do this:
1. Maintaining a backup system complete with a Processor ad OS, which shares timestamps with the main system from time to time, and in case of failure takes over.
2. **Symmetric Multiprocessing** : In this approach multiple processors run independently each having its own OS. These OS communicate with each other.
3. **Asymmetric Multiprocessing** : This approach assumes the roles of master processor and slave processors. The master processor delegates the tasks in a job to each processor and compiles the result to generate an output. 
## Symmetric vs Asymmetric multiprocessing
| S. no. | Symmetric Multiprocessing                                                                | Asymmetric Multiprocessing                                                                 |  |
| ------ | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | 
| 1      | All processors have equal precedence                                                     | Processors have a master-slave hierarchy                                                   |   
| 2      | Failure of a single processors affects the speed                                         | Failure of a slave processor affects speed, whereas of master shuts down the entire system |   
| 3      | Can have unequal division of task load                                                   | Maintains optimum division of task load                                                    |   
| 4      | I/O management is complicated as each I/O must be directed to/via the correct processors | I/O management is simpler and interacts with the main processor only                      |    
| 5      | Common in small to medium scale systems                                                  | Common in large systems where majority of time-consuming tasks involve processing I/O      |  
| 6      | Example : Multimax computer by Encore, running on custom UNIX                            | Example : Personal Computers, where tasks such as keyboard Input, Monitor control, Printer Control, Disk management are delegated to specialised processors within respective peripherals                                                                                           |    

It is important to note that the difference between systems can be due to hardware design or software design. The personal computer delegating its tasks has become so common that such delegation is no longer considered multiprocessing in tech circles.

# Distributed Systems
#looselyCoupledSystems #distributedSystems #nodes #loadSharing 
First we have to establish that distributed systems are the counterparts to the parallel systems. Distributed systems are *loosely coupled* by which I mean that the system has multiple processors each with its own minimum local resources, and communicate with one another through communication lines. The processors can vary in size and functions. The pros of building a distributed system are as follows :
1. **Resource Sharing** : In distributed systems, the processors can utilize the resources available at various remote sites that host processors. Examples include processing information on a distributed database, printing files at remote sites, using specialised hardware available at other sites.
2. **Computation Speedup** : If a computation can be broken down into sub-computations, then the OS distributes it among the various sites. This is also used to relieve overworked processors. This is called #loadSharing.
3. **Reliability** : If one site fails, the others can continue operating if sufficient redundancy is installed based on the system design. In systems composed of multiple personal computers, failure of one will not lead to system failure; whereas if a site hosts a critical hardware or system is composed of small units that each do their own critical job, the system will fail.
4. **Communication** : Each site (processor plus peripherals unit) can communicate with other sites to obtain/transfer information.
It is imperative that we differentiate between the parallel and distributed OS to get better understanding.
[Source](https://www.geeksforgeeks.org/operating-system-difference-between-distributed-system-and-parallel-system/) 

| S. No. | Parallel System                                                      | Distributed System                                                                   |
| ------ | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| 1      | Simultaneous execution of different jobs                           | A single job runs on multiple computers                                               |
| 2      | Simultaneous use of multiple computers and their resources           | Multiple computers are connected together and managed to share a job processing load |
| 3      | Faster                                                               | Slower                                                                               |
| 4      | Multiprocessor systems, ie resources are shared                      | Each processor has its own resources                                                 |
| 5      | AKA Tightly coupled system                                           | AKA Loosely Coupled System                                                           |
| 6      | Communication is done via the common resource lines                  | Communication is done via dedicated communication lines                              |
| 7      | Peripheral devices are shared within processors                      | Resources are shared upon request, no direct access,                                 |
| 8      | Minimum synchronisation between processors required, clock is shared | Many synchronisation algorithms are used                                             |
| 9      | Example : Hadoop, MapReduce, Apache Cassandra                        | Example : Beowolf clusters, High performance computing clusters                                                                                     |


# Real Time Systems
#real-timeSystem #hardReal-TimeSystem #softReal-TimeSystem 
This OS works in the case where there are rigid time constraints, or a strict precedence in job order. A real time system is considered to be working correctly only if it return the results within the time limit. In contrast to the #time-sharingSystems where timely response is desirable but not mandatory, and #batchSystem where there are no no time constraints. There are two types of real-time systems : 
1. **Hard Real-Time System** : This system guarantees that tasks are completed on time. These are used in cases where time constraints are extremely critical, like in scientific experimentation, medical imaging systems, industrial control systems. This requirement of absolute time limit compliance requires that one must eliminate all time consuming peripheries such as external storage(use RAM or ROM instead). Most advanced OS features are absent too since they reduce direct control on hardware. There is no #virtualMemory and #time-sharingSystems can never be combined with these.
2. **Soft Real-Time Systems** : Critical tasks get priority over other tasks, and retain priority until they are executed. Delays are minimised by multiprocessing. These however find limited use due to  non-strict time compliance. These use advanced OS features, and find use in multimedia, virtual-reality, rovers and submarines.

Next → [[General Structure of Operating System]]
