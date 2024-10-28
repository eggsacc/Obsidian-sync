
Created <font style="color:tomato; font-family:Consolas;">02-10-2024</font>

Tags: #c #cpp #pointers #memory

Related:  [[5) Pointers & arrays]]

****

## Memory allocation

The memory allocated to a program is divided into 4 sections.


![[Pasted image 20241002173151.png | center | 400]]


## How programs use memory

#### 1) Text (code)

This portion of memory is reserved to store the compiled machine code. It is read-only since we do not want to modify the program code during runtime (duh).

#### 2) Global / static data

Stores any global and static variables. Global variables are accessible from anywhere in the program for the entire runtime. Static variables persists across function calls, but are only accessible from within the function they are declared in.

#### 3) Stack

Whenever a function is invoked, it gets pushed onto the stack. A chunk of memory called a stack-frame is allocated to run the function and store all the local variables created within the function. Only the function at the top of the stack will be executed while those below are paused.

The image below illustrates what happens in the stack when we try to print the variables `a` and `b` by calling the function `cout <<` .


![[Pasted image 20241003111443.png | center | 600]]


Once a function finishes executing and returns, it's stack frame is popped (removed) from the stack and all the local variables are deleted.


![[Pasted image 20241004093121.png | center | 600]]


The stack has a fixed memory size that is determined during compile time. If too many functions are called during runtime, the memory limit of the stack may be exceeded, resulting in a stack overflow. This is usually caused by poorly written recursive functions, such as back-tracking algorithms without exploration limits. These functions will invoke more functions indefinitely and eventually cause the program to crash due to stack overflow.

#### 4) Heap

Also referred to as free or dynamic memory, the heap is a "spare" set of memory that could be used to store anything. Unlike stack memory, the size of heap memory could grow and shrink dynamically during runtime. The maximum size of heap memory is only hardware limited; it can keep expanding in size as long as the computer has enough memory.

Despite being much more versatile than stack memory, heap memory is considerably slower and has to be managed manually by the programmer.


## Using dynamic memory

Special functions are used to manage dynamic memory in C/C++. Note that since C++ is a superset of C, all the functions in C are also usable in C++.

|     C     |   C++    |
|:---------:|:--------:|
| `malloc`  |  `new`   |
| `calloc`  | `delete` |
| `realloc` |    -     |
|  `free`   |    -     |
#### 1) `malloc` (memory allocation)

Allocates a block of memory of a specific size from the heap, but does not initialize the memory.

**Prototype**:
````c
void* malloc(size_t size); 
````

**Return:** `void*` pointer to beginning of allocated memory block, or `NULL` if the allocation fails.

**Note:** 
- Allocated memory contains garbage values since it is uninitialized.
- We must cast the `void*` pointer to the appropriate type.

**Example:**
````c
int* ptr = (int*)malloc(sizeof(int));
*ptr = 10;

// allocate memory to array of size 20
int* ptrArray = (int*)malloc(20 * sizeof(int));
````


#### 2) `calloc` (contiguous allocation)

Also allocates dynamic memory like `malloc`, but is more used for arrays. We can specify the number of elements and size of each element for it to allocate a group of contiguous memory.

**Prototype**:
````c
void* calloc(size_t num, size_t size);
````

**Return:** `void*` pointer to beginning of allocated memory, or `NULL` if allocation unsuccessful.

**Note:** 
- Unlike `malloc`, the allocated memory is automatically initialized to all `0`s.
- Since it also returns a `void*` pointer, we need to typecast it to our array type.

**Example:**
````c
int* ptrArray = (int*)calloc(20, sizeof(int));
````


#### 3) `realloc` (reallocation)

Resizes a previously allocated block of memory. It can dynamically expand or shrink the memory block, or move it to another location if necessary.

**Prototype**:
````c
void* realloc(void* ptr, size_t newSize);
````

**Return**: `void*` pointer to the beginning of resized memory block. If the memory block is moved to another address, the contents of the old block is copied over.

**Note**:
- If the memory block is expanded, the new memory addresses are not initialized and contains garbage values.
- If `realloc` fails, the original memory block remains intact.

**Example:**
````c
int* ptrArray = (int*)malloc(5 * sizeof(int));

// increase size of array by 5 elements
int* ptrNewArray = (int*)calloc(ptrArray, 10 * sizeof(int));
````


#### 4)`free` (deallocate memory) 

After we allocate a chunk of dynamic memory using `malloc`, it remains in place for the entire lifetime of the program or until we manually clear it. `free` is used to clear a portion of dynamic memory from an address. 

**Syntax:** `free(address);`
**Return:** Non
**Note:** Do not use the pointer after freeing it as it may result in the dangling pointer error.

**Example:**
````c
free(ptr);
````


#### 5) `new` (C++ method for memory allocation)

Does the same thing as `malloc`, but automatically handles pointer typecasting.

**Syntax:** `new (type)`
**Return:** Pointer to address in dynamic memory
**Note:** No manual typecasting is required

**Example:**
````c++
int* ptr = new int;
*p = 10;

// allocate memory for array of size 20
int* ptrArray = new int[20];
````


#### 6) `delete` (C++ method for deallocating memory)

Same as `free`.

**Syntax:** `delete (ptr)`
**Return:** Non
**Note:** Add `[]` for arrays

**Example:**
````c++
delete ptr;

// for array pointers
delete[] ptrArray;
````


## Alternate uses of `realloc`

Since `realloc` is able to expand or shrink the size of allocated memory, we can use it to perform similar functions as `malloc` and `free`.

If we set the `newSize` parameter to 0, we are basically just deallocating memory. The code below does the same thing as using the `free` function to deallocate memory from `ptr`.

````c
int* ptr = (int*)malloc(sizeof(int));
int* ptr = (int*)realloc(ptr, 0);
````

`realloc` can also allocate dynamic memory like `malloc` if we set the pointer parameter to `NULL`. The code below will allocate a new chunk of dynamic memory to store the integer 10.

````c
int* px = (int*)realloc(NULL, sizeof(int));
*px = 10;
````


## Memory (un)safety

C/C++ is memory unsafe by default due to the fact that we can access any and all memory even if it is unused.

To illustrate how unsafe it is, let's allocate some memory to an array of size `n` and set the elements to count up from 1 to `n`.

````c
int n = 3;
int* arr = (int*)calloc(n, sizeof(int));

for(int i = 0; i < n; i++){
	arr[i] = i;
}
````

What do you think will happen if we tried to iterate through the elements in `arr` after deallocating it using `free`?

````c
free(arr);

for(int i = 0; i < n; i++){
	printf("%\n", arr[i]);
}

// OUTPUT: -1585708720 -1585708720 -1585708720
````

The program does not return an error, but rather just outputs a bunch of garbage values. This is because we are trying to access deallocated memory, which leads to unexpected outcomes.

In fact, the program would not raise an error even if we tried writing to unallocated memory, For example, we can still set `arr[2] = 10` after deallocating the array and the compiler wouldn't see anything wrong with it.

This is one of the biggest issues with C/C++; the ability to access and modify any memory address even if it is uninitialized or allocated to any variables. In more complex projects with multiple pointers, we may accidentally reference a pointer that has been already been deallocated and run into unexpected errors.