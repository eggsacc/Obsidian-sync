
Created <font style="color:tomato; font-family:Consolas;">30-09-2024</font>

Tags: #c #cpp #pointers

Related: NIL

****

## What are pointers?

When we create a variable in our code, the compiler assigns some memory space to it and takes note of the **location** in memory that it is stored. We can create a pointer variable that **stores the memory address** of some other variable, and use it to directly access or manipulate the location it "points" to.

## Reference and de-reference operator

There are 2 operators used in C/C++ for pointers:

1) `*` : De-reference operator, used to access contents of a memory address.
2) `&` : Reference operator, used to retrieve memory address of variable.

When also use the `*` operator to signify that a variable is a pointer during declaration. 

````c++
int a = 10;
char b = "a";

int * pa;
char * pb;
````

The pointer variable has to be the same type as the variable it is pointing to. In the example above, we create a pointer `pa` of type `int` since we want to point to `a` which is an integer, and a pointer `pb` of type `char` since `b` is a set of characters.

> The naming convention for pointers is to add a "p" before the name of the variable it is pointing to. So if the variable is called `Alpha` , for example, the pointer to it will be named `pAlpha`.

We can use the reference operator `&` to retrieve the memory address of variables `a` and `b` and store it in the pointers:

````c++
int a = 10;
char b = "a";

int * pa = &a;
char * pb = &b;

cout << pa << endl;
cout << (void*)pb << endl;

/* OUTPUT:
0xf19cfffb3c
0xf19cfffb3b
*/
````

We can see that `a` is stored at `0xf19cfffb3c` and `b` is stored at `0xf19cfffb3b`.

Now, to access the **contents** at a memory location, we use the de-reference operator `*` on the address.

````c++
cout << *pa << endl;
cout << *pb << endl;

/* OUTPUT:
10
a
*/
````

We can also modify the contents at a memory location:

````c++
*pa = 12;
cout << *pa << endl;

/* OUTPUT:
12
*/
````


