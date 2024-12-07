
Created <font style="color:tomato; font-family:Consolas;">24-11-2024</font>

Tags: 

Related: NIL

****

## Finding absolute value

To find the absolute value of some number without using built-in functions like `abs()`, one might try to use a simple `if` statement to check if a number is negative and negate it's sign if `true`.

````c
int _abs(int x)
{
	return x < 0 ? -x : x;
}
````

However, we usually want to avoid using branching statements if we want our code to run fast. Branching statements refers to code that provides the CPU with 2 (or more) execution paths. In this case, the `if` statement disrupts the CPU's instruction pipeline since it has to look at the value of `x` and make a decision on what to do next.


## Optimization using binary

Since most modern computers use Two's complement to represent signed numbers, we can exploit the sign extension property to find the absolute value without branching.

````c
int _abs(int x)
{
	int mask = x >> (sizeof(int) * 8 - 1);
	return (x + mask) ^ mask;
}

// sizeof(int) is based on compiler & system architecture
// Assuming integer size of 64-bits:
int _abs(int x)
{
	int mask = x >> 63;
	return (x + mask) ^ mask;
}
````

The function first produces a `mask` using the signed bit (MSB). Sign extension in Two's complement means that the MSB will be copied over to all the bits when we right-shift the number by it's binary length. If the MSB is `0` (positive), the mask would be `0`; if the MSB was `1` (negative), the mask would be `-1` (`1111111111111111` by Two's complement). Note that if the type of compiler used is known, the `sizeof(int) * 8` portion of the code could simply be a constant.

The function then returns the value of `(x + mask) XOR mask`. `x + mask` is simply `x + 0` if x is positive, and `x - 1` otherwise. The `XOR` bitwise operation flips all the bits in `x - 1` if `x` is negative, and does nothing if `x` is positive, which produces the Two's complement of the negative of `x` only if `x` is negative.