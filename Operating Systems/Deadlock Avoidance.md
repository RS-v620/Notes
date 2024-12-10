This is an attempt to compile all the deadlock prevention strategies discussed so far to make one universal deadlock avoider that works in al conditions.

We can avoid deadlocks, but only if certain information is available in advance. 
This section examines  ways to avoid deadlock by careful resource allocation

### The Banker's Algorithms for a Single Resource
#bankersAlgorithm
It is modeled on the way a small-town banker might deal with a group of customers to whom he has granted lines of credit. The banker does not necessarily have enough cash on hand to lend every customer the full amount of each one’s line of credit at the same time. In Fig. 3-13(a) we see four customers, A, B, C, and D, each of whom has been granted a certain number of credit units (e.g., 1 unit is 1K dollars). The banker knows that not all customers will need their maximum credit immediately, so he has reserved
only 10 units rather than 22 to service them. He also trusts every customer to be able to repay his loan soon after receiving his total line of credit (it is a small
town), so he knows eventually he can service all the requests.

![[Pasted image 20231011064242.png]]
Each part of the figure shows a state of the system with respect to resource allocation, that is, a list of customers showing the money already loaned (tape drives already assigned) and the maximum credit available (maximum number of tape drives needed at once later). A state is safe if there exists a sequence of other states that leads to all customers getting loans up to their credit limits (all processes getting all their resources and terminating).

The banker’s algorithm considers each request as it occurs, and sees if granting it leads to a safe state. If it does, the request is granted; otherwise, it is postponed until later. To see if a state is safe, the banker checks to see if he has enough resources to satisfy some customer. If so, those loans are assumed to be repaid, and the customer now closest to the limit is checked, and so on. If all loans can eventually be repaid, the state is safe and the initial request can be granted.

## Banker's Algorithm for Multiple Resources

![[Pasted image 20231011065351.png]]
In Fig. 3-15 we see two matrices. The one on the left shows how many of each resource are currently assigned to each of the five processes. The matrix on the right shows how many resources each process still needs in order to complete. As in the single resource case, processes must state their total resource needs before executing, so that the system can compute the right-hand matrix at each instant.

The three vectors at the right of the figure show the existing resources, E, the possessed resources, P, and the available resources, A, respectively. From E we see that the system has six tape drives, three plotters, four printers, and two CDROM drives. Of these, five tape drives, three plotters, two printers, and two CDROM drives are currently assigned. This fact can be seen by adding up the four resource columns in the left-hand matrix. The available resource vector is simply the difference between what the system has and what is currently in use.

Given these two matrices and the vectors. the algorithm is :
1. Look for a row R, whose unmet resource needs are all smaller than or equal to A. If no such now exists, the system WILL run into a deadlock later.
2. Assume the process of the row chosen requests all the resources it needs ( which is possible most of the time ) and finishes. Mark that process as terminated and add all its resources to the A vector
3. Repeat for all processes until all processes are marked terminated, in which case the initial state was safe, and if a deadlock occurs, it was not.

In summary, the schemes described earlier under the name ‘‘prevention’’ are 
overly restrictive, and the algorithm described here as ‘‘avoidance’’ requires information that is usually not available.


END of UNIT