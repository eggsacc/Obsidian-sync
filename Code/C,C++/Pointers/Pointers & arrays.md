
Created <font style="color:tomato; font-family:Consolas;">01-10-2024</font>

Tags: #c #cpp #pointers 

Related: [[Introduction to pointers]] | [[Pointer arithmetic & types]] | [[Pass by reference]]

****

# How arrays are stored

````c++
int arr[5];
````

When we create an array, contiguous portions of memory are allocated to the variables stored within. In the above example, the array of 5 integer elements will be stored as such:

| Memory location | 0x05 - 0x08 | 0x09 - 0x0C | 0x0D - 0x10 | 0x11 - 0x14 | 0x15 - 0x18 | ... |
| :-------------: | :---------: | :---------: | :---------: | :---------: | :---------: | :-: |
|  Array element  |   arr[0]    |   arr[1]    |   arr[2]    |   arr[3]    |   arr[4]    |  -  |
The array occupies a total of 20 bytes since each integer element is assigned 4 bytes.


## Array pointer properties

The array name `arr` itself is actually the memory address where the array starts. 

````c++
int arr[5];
cout << arr; // OUTPUT: 0x05
````

We can also find the address of any element:

````c++
int *p = &arr[1]; // points to address of element 1
cout << p; 
// OUTPUT: 0x09
````

Since the elements in the array are stored contiguously and in order, we can also use arithmetic operations to find the address of element `[i]`. 

````c++
int *p = &arr;
cout << "address of element 0:" << p; // 0x05
cout << "address of element 1:" << p + 1; // 0x09
cout << "address of element 2:" << p + 2; // 0x0D

// Generally, to access element i, we can just use
*(p + i)
````


## Arrays as function arguments

Let's create a simple function that accepts an array as a input parameter and outputs the number of elements.

The `sizeof` method in C returns how big a variable is in bytes. Hence, we should be able to calculate the number of elements in an array by dividing the total size of the array by the size of one element.

````c++
void countElements(int arr[]){
    int element_count = sizeof(arr) / sizeof(arr[0]);
    cout << element_count << endl;
}

main(){
    int arr[] = {1, 2, 3, 4, 5, 6};
    countElements(arr);
    return 0;
}

// OUTPUT: 2
````

However, the output is 2 when there are clearly 6 elements in the array. Why is this so?

When we pass an array as a function argument, the array **decays** into a **pointer variable**. In the code above, the array `arr[]` is converted to a `int*` pointer when we pass it to the `countElements` function. Hence, the `sizeof(arr)` within the function actually returns the size of the ***pointer***, which is 8 bytes in a 64-bit system, instead of the array size.

There is no way of knowing the size of an array within the function since it is always passed by reference. Hence, we usually create another input argument `size` to specify the size of the array for the function.

## Accessing array elements in function

We can access the elements stored in the array as per usual within the function:

````c++
void enumerateElements(int arr[]){
	for(int i = 0; i < 4; i++){
		cout << "Element" << i << "is" << arr[i];
	}
}

int main(){
	int array[] = {2, 4, 6, 8};
	enumerateElements(array);
	return 0;
}
````

In other languages like Python, if we tried to access an element outside the range of the array, like say `arr[5]` when the array only has 4 elements, it returns an `out of range` error.

However, we mentioned earlier that there is no way of knowing the size of the array within the function since it is passed as a pointer in C. So what if we tried to access elements outside the range of the array in the function?

````c++
void enumerateElements(int arr[]){
	for(int i = 0; i < 6; i++){
		cout << "Element" << i << "is" << arr[i];
	}
}

int main(){
	int array[] = {2, 4, 6, 8};
	enumerateElements(array);
	return 0;
}
````

The array only has 4 elements, but we are trying to print `arr[4]` and `arr[5]` in the loop, which is out range. However, the program does not return any errors since it is unaware of how big the array even is. All it does it look at the next memory address from the pointer address and returns some junk value for `arr[4]` and `arr[5`]. 

Hence, it is good practice to also tell the function the size of the array so we can implement boundary checks within the function to prevent it from accessing memory outside of the array and causing the program to crash.