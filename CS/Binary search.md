
Created <font style="color:tomato; font-family:Consolas;">01-11-2024</font>

Tags: #algo

Related: NIL

****

## Principle

Binary search is an efficient algorithm for searching through large, sorted arrays and lists using the divide and conquer principle. 

The most important condition when applying binary search is that the input list or array must be sorted in ascending order. When searching, the array is constantly being cut in half and having it's "middle value" compared against the target value. If the target value is larger than the center value, we discard the left half portion and only search towards the right; If the target is smaller than the center value, we only search the left.

Imagine a book of student names with thousands of entries sorted in alphabetical order, from `A`-`Z`, and we want to find the name `Taliyah`. Using the binary search approach, we first split the book down the center and look at the name on the center page. Suppose the name in the middle is `Malcom`: since we know that the letter `T` is **after** `M`, we can throw away the left half of the book that contains the irrelevant names starting with `A`-`M`. 

We then repeat the process of splitting the right-half of the book in half again: this time, the name in the middle is `Viven`, so we discard the right-half since `T` comes before `V`. We keep repeating the process until the name in the center page is `Taliyah` itself, and alas, we have finally found her.


## Implementation in C

To write a binary search function in C, the function has to be provided with the following arguments:

1) Array to search
2) Index range to search: lower and upper bound
3) Target value

````c
int binarySearch(int* arr, int low, int high, int target)
{
	;
}
````

The `low` and `high` values indicate the region of indexes to search. The position of the middle index `mid` given the a range (`low`, `high`) is `low + (high - low) / 2`. Note that all positions are integers, so any odd-number divisions will be rounded down.


![[Pasted image 20241101224006.png#invert]]


The value at in the `mid` position is compared to the target value. If it is smaller than the target, we update the `low` and `high` values to match the new range to search.


![[Pasted image 20241101225139.png#invert]]

Update `low` to narrow search range:

![[Pasted image 20241101230006.png#invert]]

Writing in code:

````c
int binarySearch(int* arr, int low, int high, int target)
{
	while(low <= high)
	{
		int mid = low + (high - low) / 2;
		
		if(arr[mid] == target)
		{
			return mid;
		}
		
		if(arr[mid] < target)
		{
			low = mid + 1;
		}
		
		else
		{
			high = high - 1;
		}
	}
	return -1;
}
````

The function returns `-1` if the target value is not present in the given array. Otherwise, it returns the index of the target value in the array.