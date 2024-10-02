
Created <font style="color:tomato; font-family:Consolas;">02-10-2024</font>

Tags: 

Related: NIL

****

# Memory allocation

The memory allocated to a program is divided into 4 sections.


![[Pasted image 20241002173151.png | center | 400]]


1) **Text (code):** This portion of memory is used to store the program code, or the instructions

2) **Static / global variables:** This portion of memory is allocated to any static / global variables in the program and is accessible for the entire lifetime of the program.

3) **Stack:** Local variables created during functions calls are stored here. These variables only live for the during of function execution, and is deleted afterwards.

4) **Heap:** 

#### Example

Consider the following program with 2 functions, `Square()` and `SquareOfSum()`.

````c++
#include <iostream>

using namespace std;

int total;

int Square(int x){
    return x*x;
}

int SquareOfSum(int x, int y){
    int z = Square(x + y);
    return z;
}

int main()
{
    int a = 4, b = 8;
    total = SquareOfSum(a, b);
    cout << "OUTPUT: " << total << endl;

    return 0;
}
````

Let's step through everything that happens in the memory when we run the code:

1) The `total` variable is stored in the global/static memory
2) The `main` function is placed in the stack to be executed. The amount of memory allocated for the function execution is called a **stack-frame**, and all the local arguments, variables etc. are stored within this frame.
3) The `main` functions calls the `SquareOfSum()` function. The `SquareOfSum` function gets placed in it's own stack-frame on top of the `main()` stack-frame; Only the top function in the stack will execute at any time, the other functions below are paused.
4) The `SquareOfSum()` function calls the `Square()` function, which again gets placed in a stack frame at the top.

At this point, the memory looks like this:

![[Pasted image 20241002204711.png | center | 600]]


The function `Square()` is at the top of the stack, so it is being executed while other functions below are paused.

After `Square()` finishes executing and returns a value, it is removed from the stack and all the local variables stored in it's stack-frame are also deleted.

