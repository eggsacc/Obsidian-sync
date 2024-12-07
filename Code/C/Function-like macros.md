
Created <font style="color:tomato; font-family:Consolas;">19-10-2024</font>

Tags: #c

Related: NIL

****

## What are macros?

In C, a macro is a piece of code that is replaced with the value of the macro during compilation. Macros are defined by the `#define` directive.

If we create a macro `#define MAX_VALUE 20`, the compiler will automatically replace all instances of `MAX_VALUE` in the code with the value 20.

We can also create macros like `#define SayHello printf("Hello there!);`. The compiler will replace any `SayHello` statements in the code with the `printf("Hello there!);`.

````c
#define MAX_VALUE 20
#define MIN_VALUE 10
#define SayHello printf("Hello there!);

int main()
{
	printf("%d", MAX_VALUE); // interpreted as printf("%d", 20)
	printf("%d", MIN_VALUE); // interpreted as printf("%d", 10)
	SayHello // interpreted as printf("Hello there!);
}
````


## Function-like macros

Macros are not necessarily just a constant. We can introduce some sort of input variable for macros to make it act like a simple function.

For example, we can create a macro `area` to calculate the area of a triangle:

````c
#define area(base, height) 0.5 * base * height

int main()
{
	int base_length = 10;
	int height = 15;
	int triangle_area = area(base_length, height);
	// Interpreted as:
	// int triangle_area = 0.5 * base_length * height;
}
````

Whenever the compiler encounters the `area(base, height)` macro, it gets replaced with `0.5 * base * height`, where the `base` and `height` values are copied over.


## Benefits

Using function-like macros to replace simple functions is often beneficial since it does not require any overhead.

To understand why there is overhead during function calls, let's examine the low-level processes that occur:

````c
float area(base, height)
{
	return 0.5 * base * height;
}

int main()
{
	int area = area(10, 20);
}
````

1) The current instruction pointer address is pushed onto the stack, so the CPU knows where to resume executing the code after the function execution completes.
2) The instruction pointer points to the function.
3) Function arguments are pushed onto the stack.
4) A function stack frame is created where all the local variables like `base` and `height` are created.
5) After the function returns, it's stack frame needs to be popped from the stack to free the memory space for future use.
6) The instruction pointer points back to the stored address and resumes executing the main code.

However, if we substituted the function with a macro `#define area(base, height) 0.5 * base * height` instead, almost all of this additional steps are eliminated, which makes the code run slightly faster.


## Other examples

#### Strings

The `#` operator will turn a parameter into a string literal. It is then concatenated with the rest of the string in `printf`. The arguments can also be a character array.

````c
#define output(s1, s2) printf(#s1 "middle %s\n", s2);
char arr[] = "abcdefg";
output(gay, arr)
````


#### Ternary operators

Ternary operators are very commonly used in macros for conditional statements. For example, the `constrain()` function that comes with Arduino is actually a macro that looks like this:

````
#define constrain(val, low, high) ((val)<(low)?(low):((val)>(high)?(high):(val)))
````


#### More complex operations

Longer, multi-line operations could be also written as a macro within a pair of `({ })`, with a backslash `\` after each line.

````c
#define FIND_MAX(array, length) ({ \
	typeof(array[0]) max = array[0]; \
	for(int i = 1; i < length; i++) \
		if(array[i] > max) \
			max = array[i]; \
	max; \
})
````


## Disadvantages

Macros are just text replacement, and we have to be careful on how we implement it in our code. It works quite differently from functions, and cannot be referenced by pointers, so any bugs in a macro could produce weird results. 

Macros are most suitable for defining constants, or very simple/repetitive functions.