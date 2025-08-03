#sorting #divide_and_conquer
Quick sort is based on divide-and-conquer methods, picking an element as `pivot` and partitioning the given array around it by placing it in the correct position. The around the pivot are two unsorted sub-arrays, on which `QuickSort` can be applied again.

The procedure is :
1. Choose a Pivot : Select an element from the array as a pivot. This can be a random index as well.
2. Partition the array: Rearrange the array around the pivot. After partitioning, all the elements smaller than the Pivot will be on its left, and all larger elements will be on its right. The pivot is then at its correct position.
3. Recursively call the process on the two newly formed sub-arrays to the left & right of pivot.
4. Base Case: The recursion stops when there is only one element left in the sub-array, as a single element is already sorted.
>[!Note] Choosing the middle point as pivot turns out be better choice overall because of logarithmic recursion

The key process in quick-sort is a `partition()`.  The following three approaches are usually taken, all with $O(n)$ time requirement.
1. **Naive Partition**: We create copy of the array. We put all smaller elements first, then all greater and copy the result to the original array. This taken additional $O(n)$ space.
2. **Lomuto Partition**: We keep track of index of smaller elements, and keep swapping. Simple. The tracking variable ends up 1 less than final index of the pivot.
3. **Hoare's Partition**: Fastest of all. We traverse array from both sides and keep swapping greater elements on left with smaller on right until the array is not partitioned.

The algorithms  is displayed in the images below:
```media-slider
---
carouselShowThumbnails: false
thumbnailPosition: bottom
captionMode: overlay
autoplay: true
enhancedView: true
transitionEffect: flip
---
![[{3AA23448-5FCF-4A4D-8539-50B975EB671E}.png]]
![[{12036631-148F-4C5A-8505-466E7EC49495}.png]]
![[{60556E22-393C-463A-AA27-8DDB1C2AE889}.png]]
![[{95BD455E-62E8-4E15-BEAC-3F94BF6838BA}.png]]
![[{14D01151-1DC9-4CC5-B767-5198110ACA78}.png]]
![[{8415B873-52AA-478B-A0FA-49F2CA66AFF8}.png]]
```

The program for doing so ( by the `Lomuto Partition` method) is as follows:

```python
def partition(arr, low, high):
	pivot = arr[high]

	##-- Index of the smaller element and indicates
	##-- the right position of pivot found so far
	i = low-1

	##-- Traverse arr[low:high] and move all smaller 
	##-- elements to the left side. Elements from low
	##-- to i are smaller in every iteration

	for j in range(low, high):
		if arr[j] < pivot:
			i+=1
			arr[i], arr[j] = arr[j], arr[i]
	##-- Move pivot after smaller elements and return 
	##-- its position. At this point all elements below
	##-- and at i are <= arr[high].
	arr[i+1], arr[high] = arr[high], arr[i+1]
	return i+1
```

```python
def quickSort(arr, low, high):
	if low<high:
		pivot_position = partition(arr, low, high)

		##-- Recursively partition smaller sub-arrays
		quickSort(arr, low, pivot_position - 1)
		quickSort(arr, pivot_index + 1, high)
```

Best case : $\ohm(n\cdot log_2 n)$ , when pivot divides the array in two equal halves
Average case: $\theta(n\cdot log_2 n)$ 
Worst : $O(n^2)$, when arrays are sorted

Space: $O(n)$, due to recursive call stack

## **Advantages of Quick Sort**

- It is a divide-and-conquer algorithm that makes it easier to solve problems.
- It is efficient on large data sets.
- It has a low overhead, as it only requires a small amount of memory to function.
- It is Cache Friendly as we work on the same array to sort and do not copy data to any auxiliary array.
- Fastest general purpose algorithm for large data when stability is not required.
- It is [tail recursive](https://www.geeksforgeeks.org/tail-recursion/) and hence all the [tail call optimization](https://www.geeksforgeeks.org/quicksort-tail-call-optimization-reducing-worst-case-space-log-n/) can be done.
