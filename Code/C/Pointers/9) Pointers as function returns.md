
Created <font style="color:tomato; font-family:Consolas;">05-10-2024</font>

Tags: #c #cpp #pointers #memory 

Related:  [[8) Pointers & dynamic memory]]

****

## Returning a pointer

let's write a simple function that returns a pointer to it's output value instead of the value itself.

````c
#include<stdio.h>

int* AddTwoNum(int* a, int* b)
{
	int result = *a + *b;
	return &result;
}

int main()
{
	int a = 10, b = 5;
	int* ptrResult = AddTwoNum(a, b);
	printf("%d", *result);  // OUTPUT: 15
	
	return 0;
}
````

The code seems to work fine and calculates the correct output 15. However, if we call any other function **before** printing the output, it produces an unexpected output.

````c
#include<stdio.h>

void randomFunc()
{
	printf("Hello! i'm gay");
}

int* AddTwoNum(int* a, int* b)
{
	int result = *a + *b;
	return &result;
}

int main()
{
	int a = 10, b = 5;
	int* ptrResult = AddTwoNum(a, b);
	randomFunc();
	printf("%d", *result);  // OUTPUT: -1585708720
	
	return 0;
}
````

To understand why this happens, we need to understand what is going on in the program memory.

When the `main()` function calls `AddTwoNum()`, the function is pushed onto the stack and in it's stack frame, the local variable `int result` is created. The function then returns the address of this local variable `&result`, which is stored in the pointer `ptrResult` in `main()`.


![[Pasted image 20241005232510.png#invert | center | 600]]


However, when the `AddTwoNum()` function returns, it is popped off the stack and the local variable `result` is also deleted.


![[Pasted image 20241005232846.png#invert | center | 600]]


The address that the pointer `ptrResult` points to no longer exists! 

However, popping a function off the stack is basically making the chunk of memory it occupied available for allocation again. The values stored in this chunk of memory is not erased, but rather over-written by new variables.

Hence, even though the `ptrResult` is technically referencing unallocated memory, it can still read the left-over value from before the function was popped. This is why the first code seemed to work alright.

However, if we called the `randomFunc()` **before** printing the result, it will get pushed onto the stack in the position that `AddTwoNum()` used to be. This would completely overwrite the contents of where `result` used to be stored, and when we try to de-reference it, we get some garbage value instead.


![[Pasted image 20241005234057.png#invert| center | 600]]
