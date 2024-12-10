The processes in the system can execute concurrently, and must be created and deleted dynamically. Thus, the operating system must provide a mechanism for process creation and termination.

### Process Creation
#parentProcess #childProcess #childrenProcess
A process may create several new processes, via a **create-process system call**, during the course of execution. The creating process is called a parent process, whereas the new processes are called the children of that process. Each of these new processes may in turn create other processes, forming a tree of processes.

Resource allocation to the children processes, aka sub-processes is either
1. New resources are allocated by the OS
2. A portion of the parent's resources are allocated. This covers for overload of system when child makes too many children processes.

The child may receive its input from the parent, or it can request new inputs.
When the parent creates a child :
1. Either parents continue to execute concurrently with the children
2. The parents wait until some/ all of its children have terminated.

In terms of address space of the child process :
1. The child process may be duplicate of the parent
2. The child has a program loaded in it.

Any version of these 3 can be made and most modern OS allow all combinations depending on requirement.

### Process Termination
#exitSystemCall #waitSystemCall #cascadedTermination
A process terminates when it finishes executing its last statement and asks the
operating system to delete it by using the ***exit system call***. The process may return data (output) to its parent process (via the ***wait system call***). All of the resources of the process, including physical and virtual memory, open files, and I/O puffers, are deallocated by the operating system.

There are also cases where parents might terminate the child process's execution by using #abort system call because:
1. The child may have exceeded allowed resource use
2. The child's task is no longer required
3. The parent is exiting, and OS demands the child be exited first.

The last one is called **Cascaded termination** and is initiated by the OS.

Next → [[Inter Process Communication]] 