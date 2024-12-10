 Four conditions must hold for there to be a deadlock
1. **Mutual exclusion condition** : Each resource is either currently assigned
to exactly one process or is available.
2. **Hold and wait condition** : Processes currently holding resources that were granted earlier can request new resources.
3. **No preemption condition** :  Resources previously granted cannot be forcibly taken away from a process. They must be explicitly released by the process holding them.
4. **Circular wait condition** :  There must be a circular chain of two or more processes, each of which is waiting for a resource held by the next member of the chain.

All of these conditions are required at once for deadlock to occur, else impossible. We will try to negotiate these conditions to break/avoid deadlocks.

Next → [[Resource Allocation Graphs]]
