
Created <font style="color:tomato; font-family:Consolas;">30-09-2024</font>

Tags: #C #Cpp #pointers 

Related: [[Introduction to pointers]]

****

# How variables are stored

We know that pointers are strongly-typed, meaning we must explicitly specify the type of variable the pointer is pointing to. Why can't we just create some generic pointer that can point to any type of variable in memory?

Let's first explore how variables are stored in memory. Depending on the type of variable, it is assigned a specific number of bytes of memory. Some common examples are:

- `bool`: 1 byte
- `char`: 1 byte
- `int`: 4 bytes
- `float`: 4 bytes
- `double`: 8 bytes
- `long`: 8 bytes

Each memory location is 1 byte. To store a integer that is 4 bytes, the compiler will assign 4 bytes of memory to the integer variable:

| Memory address: |  0x25  |  0x24  |  0x23  |  0x22  |
|:---------------:|:------:|:------:|:------:|:------:|
|      Byte       | Byte 3 | Byte 2 | Byte 1 | Byte 0 |
When we create a pointer to point to the memory address of this integer, it will point to the address of byte 0 `(0x22)`.

````c++
int a = 1024
int* ptr = &a;
cout << ptr << endl;

// OUTPUT: 0x22
````

Now I want to access the integer using the de-reference operator `*`. If we do not specify the type of variable for the pointer, it does not know how many more memory addresses the variable extends into. Hence, we must tell the pointer what type of variable it is pointing to for it to figure out how many memory addresses the variable spans.

#### Example: de-referencing `int` with `char*`

Let's create an integer `a = 1025`(binary `1000000001`). This is what `a` looks like in memory:

| Memory address |   0xe4   |   0xe3   |   0xe2   |   0xe1   |
|:--------------:|:--------:|:--------:|:--------:|:--------:|
|      Bits      | 00000000 | 00000000 | 00000010 | 00000001 |
Now we do the usual stuff and create a pointer `ptr` pointing to the address of `a`. However, when we de-reference the pointer, we will use a `typecast` to change the pointer `ptr` type to a `char` instead.

````c++
int a = 1025;
int* ptr = &a;
char* ptr_char = (char*) ptr; // typecast pointer to char*

cout << *ptr_char << endl; 

// OUTPUT: 1
````

The output is 1 instead of 1025 due to the pointer being of the type `char`. As aforementioned, the datatype `char` only uses 1 byte of memory, so the pointer of type `char` will only extract 1 byte of data from the memory location of `a`, which is `00000001`, or simply 1 in decimal.


## What happens if we add some number to a pointer?

````c++
int a = 0;
int* ptr = &a

cout << ptr << endl;
cout << ptr + 1 << endl;

/* OUTPUT:
0xf9187ff854
0xf9187ff858
*/
````

The original address the pointer `ptr` points to is `0xf9187ff854`. When we add 1 to the address, the resulting address is not `0xf9187ff855`, but rather `0xf9187ff858`. Why is this the case?

As explained earlier, integers are assigned 4 bytes of memory. Hence, the integer variable is located from 
 `0xf9187ff854` to `0xf9187ff857`. This is why incrementing the address by 1 skips to the next available memory location 4 bytes from `a`, which is `0xf9187ff858`.

Pointer arithmetic in C++ takes the size of the data type into account. When you add `1` to a pointer, the pointer advances by the size of the type it points to, not by just one byte.

So if we were to **add 2** to a pointer pointing to a variable of the type `double`, what would the new address be?

````c++
double a = 0;
double* ptr = &a;

cout << ptr << endl;

// OUTPUT: 0xb6c4dffd50
````

The address would be incremented by 16 to `0xa5755ffe60` since the variable of type `double` takes up 8 bytes of memory.  

````c++
cout << ptr + 2 << endl;

// OUTPUT: 0xa5755ffe60
````


## De-referencing uninitialized memory

Following the code above, if we tried to access the memory location `ptr + 2` using the de-reference operator `*` , the program would actually not return an error despite it being uninitialized memory:

````c++
cout << *(ptr + 2) << endl;

// OUTPUT: 1.3136e-311
````

It will just return some garbage value from the memory location which may be the left-overs from other programs. Or in the case of an application, this will most likely cause it to crash. 

This is one of the main issues with pointers in C: the ability to access any parts of the memory even if nothing is stored in it. This leads to debugging problems since de-referencing uninitialized memory returns a random value every time and does not raise any errors during compilation.

## The `nullptr`

If we somehow want to include a pointer that points to nowhere, there is a type-safe constant called the `nullptr` that could be used to indicate it is a **null pointer**.

````c++
// create a pointer pointing nowhere;
int* ptr = nullptr;

cout << *nullptr << endl;

// OUTPUT: (nothing)
````

When we try to de-reference a `nullptr`, it will either be reported as undefined behavior by the compiler or result in a program crash. However, we now have the option to check if a pointer is a `nullptr` before de-referencing it:

````c++
if (ptr != nullptr) { 
	cout << *ptr << endl; // Dereference only if the pointer is not nullptr 
} 
else { 
	cout << "Pointer is null, cannot dereference." << endl; 
}
````


## The `void` pointer type

There also exists a pointer type called `void`. Pointers of this type does not belong to any data types (hence the name) and could not be de-referenced or be used in any arithmetic operations.

````c++
int a = 1025;
int* ptr = &a;
void ptr_void = ptr;

cout << *ptr_void << endl; // returns error: unable to dereference void ptr
cout << ptr_void + 1 << endl; // returns error: cannot perform addition
cout << ptr_void << endl; // returns address of a, 0xe1
````
