#sorting #insertionSort #On2

Intuition will be made using a deck of cards: 
==Just like how a card gets inserted into a person's forehead when thrown towards him, insertion sort inserts cards in a way humans do==
(Hayate no Gotoku)

So, the approach is:
1. Place the deck on a table and designate a space for sorted cards.
2. Pick up the first card, and place it in the designated space.
3. Repat until deck empty the following
4. Pick another card from deck,
5. Compare it with the cards already in the designated zone starting from the last card you placed, stopping when cards get lower, and place it at its right position such that the designated place is sorted

There is a 2-array approach, an in-place approach, and brute force approach. I am putting here the optimized one, from CLRS.

```
def insertionSort(arr):
	for j in range(1, len(arr)):
		key = arr[j]
		i = j-1
		while arr[i] > key and i >=0:
			arr[i+1] = arr[i]
			i -= 1
		arr[i+1] = key
```

### Working 
This is working in place. We assume that the first array element, or in the analogous case the first card, is sorted and in the right place. We then select the next card, and if it is smaller than the previous card, we place it on top, which is analogous to the array element being moved to second position, and so on.
Now understanding the logic:
We start by taking an iterator j with initial value 1, instead of 0. 
We store the value here in $key$, because it could be overwritten in the moving of elements. We start at one place less than $j$, at $j-1$ and keep checking if the number/card is greater than $key$. We keep checking until we find a place where card can be resided, in which case the loop breaks, with $i$ now pointing at a location less than the correct place. So we now finally insert the element at $i+1^{th}$ location.

### Time Analysis
- In Best case, the array is already sorted. Each inner while loop will run only once and fail the condition. The for loop will execute for $n$ time, giving a time of $O(n)$
- In Worst case scenario, the array is sorted in descending order. Each while-loop will run $j$ times where $j = n$ during iterations. The summation will be : $$ Operations\;\; = (1+1) + (2+2)+(3+3)+\; ... +(n+n) + c*n$$ $$ = 2\cdot\sum{n} + cn $$
$$ = 2\frac{(n)(n+1)}{2} + c*n$$
$$ = O(n^2) $$
- In average case where the array/ cards are shuffled well: $$ Operations\;\;=\;\;\sum n + \sum{n/2} \;\; = \;\;O(n^2)$$ 
## CLRS - Ch-2: Exercise 2.3-4 :: Implement Insertion Sort Recursively
- Q : Insertion sort can be expressed as a recursive procedure where in order to sort $A[1..n]$ , we recursively sort $A[1..n-1]$ and then insert $A[n]$ into the sorted $A[1..n-1]$. Write a recurrence and the running time for this version of insertion sort.

Okay, so the idea is to make a `insert()` and one `insertionSort()` function. Insert will place the element in the right position in the array, while `insertionSort()` will call the `insert()` recursively, and itself recursively to do the sorting,,, I hope so.

So thinking recursively, the insert function will receive a sorted array, where the last element needs to be placed in the correct place. I will pass the arr, and implement the standard checking function. I would need to pass the length of the sorted array though to ensure insert sees smaller and smaller array when expanding recursion.
```python
def insert(arr:list, end:int):
	if end<2: return

	insertant = arr[end]

	for i in range(end-1,-1,-1):
		if arr[i] < insertant:
			arr[i+1]=insertant
			break
		arr[i+1] = arr[i]

```

```python
def insertionSort(arr:list,size:int):
	if size<1 : return

	insertionSort(arr, size-1)
	insert(arr, size)
	
```
