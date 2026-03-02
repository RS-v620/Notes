#algorithm
Developed by **Edsger Dijkstra**, the Dutch National Flag Algorithm sorts an array of three distinct values *say 0,1,2* in a single $O(n)$ pass with $O(1)$ storage.
It utilizes three pointer, namely:
- `low` = 0
- `mid` = 0
- `high` = `len(array) - 1`

### Traversal
Iterate while `mid`$<=$ `high`:
```py
if arr[mid] == 0:
	arr[low],arr[mid] = arr[mid], arr[low]
	low+=1
	mid+=1
```
```
if arr[mid] == 1:
	mid+=1
```
```
if arr[mid]==2:
	arr[mid], arr[high] = arr[high], arr[mid]
	high -= 1
```

This neat method gives it
- $Time :  O(n)$ &
- $Space : O(1)$

			
