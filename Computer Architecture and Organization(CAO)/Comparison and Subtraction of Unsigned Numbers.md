In hardware implementation, subtraction can't work on the principle of borrowing. Therefore we use a different method based on $n$ digit unsigned numbers $M-N(N \neq 0)$ in base $r$ can be done as follows:
1. Add the minuend $M$ to the $r's$ complement of the subtrahend $N$. This performs $M+(r^n-N)=M-N+r^n$.
2. If $M\geq N$, the sum will produce an end carry $r^n$ which is discarded, and what is left is the result $M-N$. 
3. If $M<N$, the sum does not produce an end carry and is equal to $r^n-(N-M)$, which is the $r's$ complement of $(N-M)$. To obtain the answer in a familiar form, take the $r's$ complement of the sum and place a negative sign in front. 
When subtracting with complements, a negative result is identified by absence of an and carry and a complemented result.

>[!info] I am doubtful about the completion of this section

Next → [[Addition]]
