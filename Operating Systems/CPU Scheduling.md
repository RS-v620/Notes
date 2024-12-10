Fundamental in #multiprogramming OS. Ensure optimum CPU usage.

### CPU-I/O Burst Cycle

The success of CPU scheduling depends on the following observed property of
processes: Process execution consists of a cycle of CPU execution and I/O wait. Processes alternate back and forth between these two states. Process execution begins with a **CPU burst**. That is followed by an **I/O burst**, which is followed by another CPU burst, then another I/O burst, and so on.

![[Pasted image 20231010222447.png]]

An I/O-bound program would typically have many very short CPU bursts. A CPU-bound program might have few very long CPU bursts. This distribution can be important in the selection of an appropriate CPU scheduling algorithm.

### CPU Scheduler
#shortTermScheduler
Whenever the CPU becomes idle, the operating system must select one of the
processes in the ready queue to be executed. The selection process is carried
out by the **short-term scheduler** (or CPU scheduler).

### Preemptive Scheduling
#preemptiveScheduling #non-preemptiveScheduling
CPU scheduling decisions may take place under the following four circumstances : 
1. When a process switches from running state to waiting state (I/O request, or termination of child process)
2. When a process shifts from running state to ready state, in case of interrupt
3. When a process shifts from waiting to ready state, example being completion of I/O
4. When a process terminates.

For circumstances 1 and 4, there is no choice in terms of scheduling. A new
process (if one exists in the ready queue) must be selected for execution. There
is a choice/however, for circumstances 2 arid 3. 
When-scheduling takes place only under circumstances 1 and 4, we say
the scheduling scheme is #nonpreemptive. Otherwise, the scheme is #preemptiveScheduling . 
Under **non-preemptive scheduling,** once the CPU has been allocated
to a process, the process keeps the CPU until it releases the CPU either by
terminating or by switching to the waiting state. Easiest to do, no special hardware required.
Unfortunately, **preemptive scheduling incurs a cost**. Consider the case of
two processes sharing data. One may be in the midst of updating the data when
it is preempted and the second process is run. The second process may try to
read the data, which are currently in an inconsistent state.

Another component involved in the CPU scheduling function is the #dispatcher. The dispatcher is the module that gives control of the CPU to the process selected by the short-term scheduler. This function involves:
1. Switching #context.
2.  Switching to #usermode .
3.  Jumping to the proper location in the user program to restart that program.

The dispatcher should be as fast as possible, given that it is invoked during
every process switch. **The time it takes for the dispatcher to stop one process
and start another running is known as the** #dispatchLatency.

Next → [[Scheduling Criteria]] 


