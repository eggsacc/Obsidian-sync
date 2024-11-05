
Created <font style="color:tomato; font-family:Consolas;">02-11-2024</font>

Tags: #algo

Related: NIL

****

## Issue: summing subarrays

Imagine we have an array `arr`, and we want to find the maximum sum of a subarray of size `n`.

The brute force way to do so is to iterate through the array and find the sum of the next `n` elements each time.

Assuming a subarray length of 5 (`n=5`), we have to calculate the sum of the next 5 elements for each position

![[Pasted image 20241103001126.png#invert]]

It could be observed that there are many repeated and unnecessary calculations performed to find the sum of each subarray. In the example above, `3+4+6+7` are repeated calculations moving from `subarray[0]` to `subarray[1]`. 

If the subarray size was way larger at say 500, up to 498 obsolete calculations are performed each iterations, which dramatically decreases code performance.


## Sliding window

The idea behind using a sliding window to sum subarrays is to always keep a record of the previous sum, and only add/subtract values that change when we shift the window.

![[Pasted image 20241103015529.png#invert]]

## Implementation in C

Below is the code for a constant size window sliding over a circular array, returning the maximum subarray sum. The function returns `-1` if the subarray size if invalid (greater than size of main array)

````c
int maxSubarraySum(int* arr, int arr_size, int subarray_size)
{
	if(subarray_size >= arr_size)
	{
		printf("INVALID SUBARRAY SIZE");
		return -1;
	}
	int max_sum = 0;
	int prev_sum = 0;
	for(int i = 0; i < subarray_size; i++)
	{
		prev_sum += arr[i];
	}
	for(int i = 1; i < arr_size; i++)
	{
		prev_sum = prev_sum - arr[i - 1] + arr[(subarray_size + i)%arr_size];
		if(prev_sum > max_sum)
		{
			max_sum = prev_sum;
		}
	}
	return max_sum;
	
}
````