
Created <font style="color:tomato; font-family:Consolas;">31-03-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## Can functions be referenced by address?

Yes, functions in C can also be **passed by reference**, or passed around by their address. However, one major distinction between variable and functions pointers is that while a variable pointer points to it's memory address on the **stack or heap**, a function pointer points to it's memory address within the **code** (or `.text`) section of the program.


![[Pasted image 20241002173151.png | center | 400]]


This means a function pointer basically stores which part of the code to execute for any function.

## Syntax

The syntax of a function pointer is:

````c
return_type (*ptr_name)(param_types);
````

Take for example a simple `add` function. The pointer declaration would look something like this:

````c
int add(int a, int b)
{
	return a + b;
}

int (*func_ptr)(int, int) = &add;
````

The `func_ptr` could then be used just like the original function.

````c
int result = func_ptr(10, 15);
// Returns 10 + 15 = 25
````


## Characteristics

Function pointers are somewhat different to pointers of other types since they reference code segments instead of allocated variables.

### 1) Requires exact function signature

A function pointer requires the exact function signature (return type, input types) to be explicitly defined during declaration. It also cannot point to another function with a different signature.

````c
int add(int a, int b)
{
	return a + b;
}

int subtract(int a, int b)
{
	return a - b;
}

float divide(int a, int b)
{
	return (float)a / (float)b;
}

int (*fptr)(int, int);

fptr = &add;       // Works fine
fptr = &subtract;  // Works fine
fptr = &divide;    // Error: divide returns a float, not int!
````

### 2) Unable to perform arithmetic operations

Recall that a standard variable pointer is strongly typed and hence supports pointer arithmetic, such as incrementing or decrementing the pointer address. However, since function pointers do not reference any objects of a specific size, but rather just a segment of the compiled code, it does not support any arithmetic operations.

````c
// Create pointer pointing to addition function
int (*fptr)(int, int) = &add;

// Try incrementing the pointer by 1
fptr++;

int result = fptr(10, 15);
````

If the above code is executed, it leads to a segmentation fault. A segmentation fault arises when there are no executable instructions at the specified location. This happens since trying to increment or decrement a function pointer leads to undefined behavior and moves the text pointer to some random place in memory that may not contain any executable code.

### 3) Supports array-like functionalities

We can actually create an array of function pointers **if and only if** the functions all have the **same return type and input parameters**.

````c
int add(int a, int b)
{
	return a + b;
}

int subtract(int a, int b)
{
	return a - b;
}

int multiply(int a, int b)
{
	return a * b;
}

int (*fptr_arr[])(int, int) = {add, subtract, multiply};
````

Since `add`, `subtract` and `multiply` all share the same return type and input parameters, it is possible to define an array of function pointers and store all of them inside. We could then select these functions using standard array indexing operations:

````c
int a = 10;
int b = 15;

int add_result = fptr_arr[0](a, b); // 25
int sub_result = fptr_arr[1](a, b); // -5
int mul_result = fptr_arr[2](a, b); // 150
````


## Passing as function arguments

We can pass a function by reference to other functions.

````c
int add(int a, int b)
{
	return a + b;
}

void operate(int a, int b, int (*op)(int, int))
{
	return op(a, b);
}

int result = operate(10, 15, add);
````

Again, we need to ensure that the function passed in as argument has the correct return and parameter types.

Also, note that we do not need the reference operator `&` when passing a function by reference. Just like any other variables in C, they automatically decay into a pointer when passed as arguments to another function.