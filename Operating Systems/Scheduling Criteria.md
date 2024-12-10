The following are the criteria used to determine the best scheduling algorithm for the system : 
#CPUUtilization #throughput #turnaroundTime #waitingTime #responseTime
1. **CPU Utilization** : We want to keep the CPU as busy as possible, typically ranges form 40% in *light usage* to 90% in *heavy usage*
2. **Throughput** : It is the number of processes completed per unit time
3. **Turnaround time** : The time interval between submission of a process to the completion is turnaround time.
4. **Waiting time** : The CPU scheduling algorithm doesn't affect the time processor takes to execute, it affects the time process spends waiting in queue. Waiting time is the sum of times spent waiting in he #readyQueue
5. **Response time** : It is the time required to start producing output, doesn't include the time required in showing the output. Useful in cases where processes put multiple outputs even during execution. Here *trunaround* *time* does not do justice.

It is desirable to maximize CPU Utilization, throughput and minimize turnaround time, waiting time, and response time.


Next → [[Scheduling Algorithms]] 