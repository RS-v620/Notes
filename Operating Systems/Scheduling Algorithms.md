4 are shown here
#FCFS #SJF #RR #priorityScheduling #firstComeFirstServe
#shortestJobFirst #roundRobin

## First-Come, First-Serve
#FCFS
The process that requests the CPU first is allocated the CPU first. The implementation managed with a FIFO queue. When a process enters the ready queue, its #PCB is linked onto the tail of the queue. When the CPU is free, it is allocated to the process at the head of the queue. The running process is then removed from the queue.

Average waiting times are long.

We use #Gantt charts to show the timeline of processor usage for processes
For a sample of three processes that arrive in the memory with ordering :
$$P1 → P2 → P3$$
With their respective CPU burst times as :
$$24ms,\;\;3ms\;\;,3ms$$
The Gantt chart will look like:
![[Pasted image 20231010233213.png]]

This causes waiting times as : $$0ms,\;\;24ms,\;\;27ms$$With an average of : $$\frac{0+24+27}{3}\;=\;17ms$$Which can be reduced if processes come in as : $$P2 → P3 → P1$$giving an average of  :$$\frac{0+3+6}{3} \;=\;3ms$$So FCFS does not give a minimum waiting time system.
FCFS causes a #convoyEffect where faster-smaller processes are bottlenecked by the larger-slower processes.

## Shortest Job First Scheduling
#SJF 
This algorithm associates with each process the length of the next CPU burst. Each time the processor becomes available, the processor is assigned the tasks that have the smallest next CPU burst. 
Again using an example : 

| Process | Burst Time |
| ------- | ---------- |
| P1      | 6ms        |
| P2      | 8ms        |
| P3      | 7ms        |
| P4      | 3ms           |
Using SJF scheduling, the Gantt chart would look like : 
![[Pasted image 20231010234223.png]]
For an average waiting time of : $$\frac{0+3+9+16}{4}\;=\;7ms$$This algorithm is optimal if we want to reduce the average waiting time. 

The real difficulty with the SJF algorithm is knowing the length of the next CPU request. **SJF scheduling is used frequently in long-term scheduling** because length of job is known beforehand.  
It though cannot be implemented at the level of short-term CPU scheduling. There is no way to know the length of the next CPU burst.

One approach is to try to approximate SJF scheduling. We may not know the length of the next CPU burst, but we may be able to predict its value.
Using the reasonable expectation that the burst will be similar in length to the previous ones, we compute an approximation of the length and choose the next task.

**The next CPU burst is calculated as an exponential average of the measured lengths of the previous CPU bursts**
Let $t_n$ be the length of the $n^{th}$ CPU burst, and let $\tau_{n+1}$ be out predicted value, then for $\alpha$ , $0<\alpha<1$, define 
$$\tau_{n+1} = \alpha\cdot t_n + (1-\alpha)\cdot\tau_n$$
This formula IS the #exponentialAverage. 
Value of $t_n$ is the most recent information. $\tau_n$ stores past history. $\alpha$ control the relative weight of recent and past history in out prediction.

| a     | Result              |
| ----- | ------------------- |
| a = 0 | \tau_{n+1} = \tau_n |
| a = 1 | \tau_{n+1} = t_n                    |
 Usually $\alpha$ = 1/2 is used.

This recursive algorithm can be expanded.

The SFJ may be #preemptive or #nonpreemptive . The choice arises when a new process arrives at the ready queue while a previous process is executing. The new process may have a shorter next CPU burst than what is left of the currently executing process. A **preemptive SJF algorithm** will preempt the currently executing process, whereas a **non-preemptive SJF algorithm** will allow the currently running process to finish its CPU burst. **Preemptive SJF scheduling is sometimes called shortest-remaining-time-first scheduling.**
![[Pasted image 20231011001456.png]]

## Priority Scheduling
SJF is a special case of priority scheduling algorithm. A priority is associated with each process, and the CPU is allocated to the process with the highest priority. Equal-priority processes are scheduled in FCFS order.
We are assuming here that lower number means higher priority. 
![[Pasted image 20231011001600.png]]
Priorities can be defined either internally or externally. Internally defined priorities use some measurable quantity or quantities to compute the priority of a process. External criteria are set by the programmer, money etc.

Priority scheduling can be either **preemptive or nonpreemptive.** When a process arrives at the ready queue, its priority is compared with the priority of the currently running process. **A preemptive priority scheduling algorithm will preempt the CPU if the priority of the newly arrived process is higher than the priority of the currently running process. A nonpreemptive priority scheduling algorithm will simply put the new process at the head of the ready queue.**
This algorithm has the drawback that it can stall the low priority processes forever if the processor is under heavy use. Also known as #indefiniteBlocking #starvation. 
A solution to the problem of indefinite blockage of low-priority processes is #aging. **Aging** is a technique of gradually increasing the priority of processes that wait in the system for a long time.

## Round Robin Scheduling
#roundRobin 
The round-robin (RR) scheduling algorithm is designed especially for timesharing systems. It is similar to FCFS scheduling, but preemption is added to switch between processes. A small unit of time, called a **time quantum**, or time slice, is defined. A time quantum is generally from 10 to 100 milliseconds. **The ready queue is treated as a circular queue**. The CPU scheduler goes around the ready queue, allocating the CPU to each process for a time interval of up to 1 time quantum.
We use FIFO queue to store the #readyQueue . New processes are added to the tail of the ready queue. The CPU scheduler picks the first process from the ready queue, sets a timer to interrupt after 1 time quantum, and dispatches the process.

Here :
1. Either the process executed before 1 time quantum, in which case it vacates the CPU and ready queue moves on
2. It doesn't finish, and the timer interrupt arrive. The scheduler places the process at the *tail* of the queue.
![[Pasted image 20231011003530.png]]

**The RR scheduling algorithm is preemptive.**
The performance of the RR algorithm depends heavily on the size of the time quantum. At one extreme, if the time quantum is very large (infinite), the RR policy is the same as the FCFS policy. If the time quantum is very small (say 1 microsecond), the RR approach is called processor sharing, and appears (in theory) to the users as though each of n processes has its own processor running at 1/n the speed of the real processor.

While deciding the value of time quants, remember that 80% of CPU bursts should be smaller than the time quanta, rest bigger.


Next → [[Circuit Switching & Packet Switching]]
