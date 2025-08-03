#sorting 
The way how bubble sort operated by swapping elements if they are larger/ smaller to find the max/ min and placing them at the end is by doing multiple swaps in each iteration. Selection sort on the other hand operates by doing a single swap at the correct position. This is not much more efficient time wise than Bubble-Sort but there are only O(n) memory writes, making it good in situations where memory write is expensive.

The procedure is :
```python
def selecitonSort(arr):
	n = len(arr)

	for i in range(n)
		min_index = i
		
		for j in range(i+1,n):
		
			if arr[j]<arr[min_index]:
				min_index = j
				
		arr[i],arr[min_index] = arr[min_index], arr[i]
		
```

As one would expect looking at the program:
Time Complexity ; $O(n^2)$
Space Complexity : $O(1)$
