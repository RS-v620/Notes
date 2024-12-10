
## Deadlock modelling
The four conditions can be modeled using **directed graphs**. The graphs have two kinds of nodes : 
1. Processes : shown as circles
2. Resources : shown as squares

An arc form a resource node to a process node means that the resource has previously been requested by, granted to and s currently being held by that process. An arc from a process to a resource means that the process ins currently blocked and is requesting that resource. 
![[Pasted image 20231011012551.png]]

Next → [[Deadlock Detection and Recovery]] 