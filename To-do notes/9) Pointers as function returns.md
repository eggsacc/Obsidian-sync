
Created <font style="color:tomato; font-family:Consolas;">05-10-2024</font>

Tags: 

Related: NIL

****

# Returning a pointer

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

To understand why 


