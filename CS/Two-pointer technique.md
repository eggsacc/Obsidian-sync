
Created <font style="color:tomato; font-family:Consolas;">13-11-2024</font>

Tags: 

Related: NIL

****

## Finding pairs in an array

The two-pointer (or double pointer) approach is commonly used to find any pairs of elements whose sum is equal to or within a range $k$.

For example, given an array `arr = [1, 3, 6, 2, 3, 7, 8, 4, 5]`, how many pairs of numbers has a sum within the range $[\ 3, 8\ ]$ ?

The brute-force method would be for each number, loop through all the other numbers and see if their sum is within the range. This method is extremely inefficient and has a time complexity of $O(n^2)$.


## 2-pointer technique

>[!info] Note
>The array has to be first sorted in ascending order for the two-pointer technique to work.

Following the above example, let's first sort the array:
````python
arr_sorted = [1, 2, 3, 3, 4, 5, 6, 7, 8]
````

Starting from the first number which is `1`, we create 2 pointers, `left` and `right` that points to the range of values we will be summing `1` with.

![[Pasted image 20241113180947.png#invert]]

The lower limit of the given range $[\ 3, 8\ ]$ is 3. We perform a simple binary search on the array to find the least value that `1` can sum with such that the result lies above the lower range limit:

- Check the midpoint of `left` and `right`. If the sum of `1` with the midpoint value is less than 3, shift left pointer to position `midpoint + 1`.
- Else, shift right pointer to position `midpoint - 1`.

In the example above, the midpoint is `(right_index - left_index) / 2 + left_index = 4`. 

![[Pasted image 20241113181033.png#invert]]

We see that the value at the midpoint is `4`, and the sum with 1 is `1+4=5` which is larger than the lower range limit of 3. Hence, we shift the `right` pointer to `mid_index - 1`:

![[Pasted image 20241113181139.png#invert]]

We then repeat this process until the `left` and `right` pointer coincide, or are directly next to each other:

![[Pasted image 20241113181233.png#invert]]

The value `2` at index `1` is the smallest number that can be added to `1` for the sum to fall in range $[\ 3, 8\ ]$. We store this index in a variable called `start` , so `start = 1`.

Now we have to find the maximum value that could be added to `1` for the sum to be smaller than the upper limit of the range. To do so, we reset the pointers to their initial positions and repeat the binary search processes. Eventually, the pointers end up at:

![[Pasted image 20241113191120.png#invert]]

If the pointers do not coincide like the previous case, we take the `left` pointer position and store it in another variable called `end`,  so `end = 7`.

The `start` and `end` indexes represent the range of indexes where the sum of `1` and each value falls within the given range. Hence, the number of valid pairs for the first element is `end - start + 1`.

We repeat this process of finding a `start` and `end` for each element in the array, and sum the total number of valid pairs to produce the final result. Using a two-pointer technique as such decreases the time complexity from $O(n^2)$ to $O(n\log(n))$, which is a significant improvement in efficiency.


## Code

````c
int comparator(const void* p, const void* q)
{
    int l = *(const int*)p;
    int r = *(const int*)q;
    return (l - r);
}

// find the number of pairs of elements whose sum
// falls within lower & upper bounds.
int countPairs(int* nums, int numsSize, int lower, int upper) {
    qsort(nums, numsSize, sizeof(nums[0]), comparator);
    
    int pairs = 0;
    
    for(int i = 0; i < numsSize; i++)
    {
        int left = i + 1;
        int right = numsSize - 1;
		
        while(left <= right)
        {
            int mid = (right - left)/ 2 + left;
            if(nums[i] + nums[mid] < lower)
            {
                left = mid + 1;
            }
            else
            {
                right = mid - 1;
            }
        }
        int start = left;
		
        left = i + 1;
        right = numsSize - 1;
		
        while(left <= right)
        {
            int mid = (right - left)/ 2 + left;
            if(nums[i] + nums[mid] <= upper)
            {
                left = mid + 1;
            }
            else
            {
                right = mid - 1;
            }
        }
        int end = right;
		
        if(start <= end)
        {
            pairs += (end - start + 1);
        }
        
    }
    return pairs;
}

int main()
{
    int nums[] = {1, 2, 3, 3, 4, 5, 6, 7, 8};
    int numsSize = sizeof(nums) / sizeof(nums[0]);
    int lower = 3;
    int upper = 8;
    int ans = countFairPairs(nums, numsSize, lower, upper);
    printf("%d\n", ans);
    
    return 0;
}
````