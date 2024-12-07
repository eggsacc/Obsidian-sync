
Created <font style="color:tomato; font-family:Consolas;">18-11-2024</font>

Tags: #cpp

Related: NIL

****

## `new`

The `new` keyword is the C++ equivalent of `malloc`. It allocates a portion of heap memory and returns the pointer to the starting address.

Below is the syntax to allocate memory for an integer variable:

````cpp
int* ptr = new int;
````

When we allocate memory, we can also initialize the address to a set value:

````cpp
// initializes int value to 10
int* ptr = new int(10);
````

We can also allocate a block of memory to say an array:

````cpp
int* int_array = new int[10];
````


## `delete`

The `delete` keyword is the C++ equivalent of `free`. It de-allocates the heap memory at an address, making available for other uses.

To free the integer previously allocated:

````cpp
delete ptr;
````

For arrays, we have to specify that we want to destroy the entire array by adding a set of square brackets:

````cpp
delete[] int_array;
````


## Cautions

1) Never attempt to write into any uninitialized pointers. For example:

````cpp
int* ptr;
*ptr = 5;
````

Will cause the program to attempt to write into some random part of memory since `ptr` is uninitialized and could point to anywhere. Usually the operating system will detect this and block it from doing so.

2) Do not `delete` and address that has already been freed using `delete`. For example:

````cpp
delete ptr;
delete ptr;
````

This will lead to unexpected errors.

### Good practice

It is a good habit to always mark a pointer that has ben deleted as a `nullptr` so others have some way of checking if a certain pointer is still being used.

````cpp
delete ptr;
ptr = nullptr;
````