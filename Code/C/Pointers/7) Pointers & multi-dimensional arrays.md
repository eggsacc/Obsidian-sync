
Created <font style="color:tomato; font-family:Consolas;">02-10-2024</font>

Tags: #c #cpp #pointers #arrays

Related: [[5) Pointers & arrays]]

****

## 2D arrays

#### Memory allocation & creating pointer

Suppose we have an integer 2D array of size `arr[2][3]`. It can be interpreted as 2 arrays of 3 elements, so 24 bytes of memory is allocated since each integer is 4 bytes.

| Memory address | ... |   0x01    |   0x05    |   0x09    |   0x0D    |   0x11    |   0x15    | ... |
| :------------: | :-: | :-------: | :-------: | :-------: | :-------: | :-------: | :-------: | :-: |
| Array element  | ... | arr[0][0] | arr[0][1] | arr[0][2] | arr[1][0] | arr[1][1] | arr[1][2] | ... |

We know that the name of an array holds the memory address of the first element. However, in the case of a 2x3 array, the "first element" is the array `{1, 2, 3}`. Hence, when we create a pointer to the array, we need to specify that the pointer points to an array of 3 integers. This is required for it to be able to de-reference the address properly.

````c++
int arr[2][3] = {{1, 2, 3}, {4, 5, 6}};
// The 'first element' in arr is {1, 2, 3}

// we need to tell the pointer it is pointing to 3 int:
int (*ptr)[3] = arr;
````

We can access any element from the pointer:

````c++
int arr[2][3] = {{1, 2, 3}, {4, 5, 6}};

int (*ptr)[3] = arr;

cout << ptr[1][1] << endl; // OUTPUT: 5
cout << ptr[0][2] << endl; // OUTPUT: 3
````

### Arithmetic operations

Since we already told the program that the pointer is pointing to a row of 3 integers, incrementing it's value will move the pointer by 1 row (3 int, 12 bytes). 

````c++
int arr[2][3] = {{1, 2, 3}, {4, 5, 6}};
int (*ptr)[3] = arr;

cout << ptr << endl; // ptr = 0x01
cout << ptr + 1 << endl; // ptr + 1 = 0x0D (jumps 12 bytes)
````

De-referencing the pointer still returns the address of the pointer, but it is now treated as a normal `int*` pointer instead of `int* [3]`.

````c++
int arr[2][3] = {{1, 2, 3}, {4, 5, 6}};
int (*ptr)[3] = arr;

cout << *ptr << endl; // OUTPUT: 0x01
cout << (*ptr) + 1 << endl; // OUTPUT: 0x05 (only jumps 4 bytes since *ptr is int*)
cout << *(ptr + 1) + 2 << endl; // OUTPUT: 0x15
````

> Very confusing tbh


## Higher array dimensions

The same concept applies for any array dimension; we just need to create a pointer pointing to the correct array size.

Let's create a pointer to a 3D array of size 2x2x3. 

````c++
int arr[2][2][3] = {{{1, 2, 3},{4, 5, 6}}, {{7, 8, 9}, {10, 11, 12}}};

int (*ptr)[2][3] = arr;
````

The pointer is pointing to a 2D array of size 2x3 in this case.

We can access any element from the pointer:

````c++
int arr[2][2][3] = {{{1, 2, 3},{4, 5, 6}}, {{7, 8, 9}, {10, 11, 12}}};

int (*ptr)[2][3] = arr;

cout << ptr[1][1][1] << endl; // OUTPUT: 11
cout << ptr[0][0][1] << endl; // OUTPUT: 2
````

If we de-reference this pointer, the returned pointer type will be `int (*ptr)[3]`. De-referencing this pointer again will return the pointer type`int* ptr`.

````c++
int arr[2][2][3] = {{{1, 2, 3},{4, 5, 6}}, {{7, 8, 9}, {10, 11, 12}}};

int (*ptr)[2][3] = arr;

new_ptr = *ptr; // type: int* [3]
new_ptr = **ptr; // type: int*
````


## Multi-dimensional arrays as function arguments

How do we pass arrays of higher dimensions to functions?

Recall that any array will decay to a pointer variable when passed as a function argument. Hence, for higher dimension arrays, we have to specify the size of the array the pointer points to.

We create a 2D and 3D array to illustrate how they should be passed by reference to functions.

````c++
//********** 2D array ***********
void func2D(int arr[][3]){
	// do something
}
// OR:
void func2D(int (*arr)[3]){
	// do something
}

//********** 3D array ***********
void func3D(int arr[][2][3]){
	// do something
}
// OR:
void func3D(int (*arr)[2][3]){
	// do something
}

int main()
{
	int arr2D[2][3] = {{1, 2, 3}, {4, 5, 6}};
	int arr3D[2][2][3] = {{{1, 2, 3},{4, 5, 6}}, {{7, 8, 9}, {10, 11, 12}}};
	
	func2D(arr2D);
	func3D(arr3D);
	return 0;
}
````

If we pass the array without using `*` in the function argument, only the highest dimension can be left blank.