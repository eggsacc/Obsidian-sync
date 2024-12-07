
Created <font style="color:tomato; font-family:Consolas;">30-09-2024</font>

Tags: #c #cpp #pointers 

Related: [[1) Introduction to pointers]] | [[2) Pointer arithmetic & types]]

****

## Referencing pointers

When we create a pointer variable, some memory is also allocated to store the address of whatever it is pointing to. Hence, the pointer also lives in the computer memory, and has an address.

We can create a pointer pointing to another pointer.

````c++
int x = 10;
int* px = &x;
int** ppx = &px;
````

Here, we place 2 `*` before `ppx` to indicate that it is a pointer to a pointer, and store the address of pointer`px`.

We can stack even more pointers by adding more `*`:

````c++
int** ppx = &px;
int*** pppx = &ppx;
int**** ppppx = &pppx;
````

We can also de-reference a pointer to other pointers multiple times:

````c++
/* ASSUME
  &x = 0x11
  &px = 0x15
  &ppx = 0x19
  &pppx = 0x1d
 */
cout << *px; // OUTPUT: value of x, 10
cout << *ppx; // OUTPUT: address of x, 0x11
cout << **ppx; // OUTPUT: value of x, 10
cout << *pppx; // OUTPUT: address of px, 0x15
cout << **pppx; // OUTPUT: address of x, 0x11
````
