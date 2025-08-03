#sorting #bubbleSort
This is $O(n^2)$ time sorting method of in-place sorting that works by finding the highest/ lowest numbers in an array in each iteration. It simply swaps the elements if they are in the wrong order. 

The basic two for-loop implementation of Bubble Sort is below:
```python
def bubbleSort(arr):
	n = len (arr)

	for i in range(n):
		swapped = False
		for j in range(0, n-i-1):
			if arr[j] > arr[j+1]:
				arr[j], arr[j+1] = arr[j+1], arr[j]
				swapped = True
		if(swapped == False ):
			break
			
```

The swapped variable is used to exit the sorting in first iteration if the array is already sorted. And yet again:
Time Complexity : $O(n^2)$
Space Complexity : $O(1)$


# Recursive Bubble Sort
#sorting #recursion
Recursive bubble sort has no real advantage but is used for concept development in recursion. Closely observing the Bubble Sort we can see that in first iteration, for `>` operation, the largest element is placed at the end of the array. In second pass, we move the next largest element to the second last position.  So the recursive implementation is based on :
- Base case - If array is size 1, return
- Do one pass of normal bubble-sort ; this pass fixes last element of current subarray.
- Recur for all the elements except that last in the current sub-array.

Implemented as :
```cpp
#include<bits/stdc++.h>
using namespace std;

void bubbleSort(int arr[], int n)
{
	if(n==1) return;

	int count = 0;

	for(int i = 0; i < n-1 ; i++)
	{
		if(arr[i] > arr[i+1])
		{
			swap(arr[i],arr[i+1]);
			count++;
		}
	}
	if(count==0) return;

	bubbleSort(arr, n-1);
}
```