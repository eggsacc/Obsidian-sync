
Created <font style="color:tomato; font-family:Consolas;">07-02-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## Heading 

````python
def binary_search(key, lst):
	def helper(low, high):
		if(low > high):
			 return False
		mid = (low + high)//2
		if(lst[mid] == key):
			return mid
		elif(lst[mid] > key):
			return helper(low, mid-1)
		else:
			return helper(mid+1, high)
	return helper(0, len(lst) - 1)
````