#mergeSort #Onlogn
Merge sort takes a recursive approach to sorting the information. A linear approach is used to sort the smallest possible subproblem, or one element each. Then each sorted set of elements/ array is combined to make a bigger sorted array two at a time until everything is sorted.
The merging is performed by a super function that calls the linear function recursively to solve the issue.

 ==Using the cards analogy again==
Merge Approach:
- Given two deck of cards to be sorted, designate a space for storing sorted cards. The decks are assumed sorted.
- In the space, compare the two cards on top of each deck, and place the lower one (higher one in case of descending one.") into the designated space.
- Do this until a deck is empty, at which time if there are any remaining cards from a deck, place them all into the sorted region.

```python
def merge(arr1:list, low, mid, high):
	temp = []
	left = low
	right = mid+1

	while left <= mid and right <= high:
		if(arr[left] <= arr[right]):
			temp.append(arr[left])
			left += 1
		else:
			temp.append(arr[right])
			right += 1
	while left <= mid:
		temp.append(arr[left])
		left += 1
	while right<= high:
		temp.append(arr[right])
		right += 1
	for i in range(high-low):
		arr[low + i] = temp[i]
	
```
This is overlooked/ called by the `mergeSort` function that recursively calls itself and then merger the output. This is a two forked branching.

```python
def mergeSort(arr:list, low:int, high:int):

	if (low >= high ) retrun; ##Fundamental case
	mid = (low + high)//2

	mergeSort(arr, low, mid) ## Sort left half
	mergeSort(arr, mid+1, high) ## Sort Right half
	merge(arr, low, mid, high) ## Merge the sorted sub-arrays
```



