
Created <font style="color:tomato; font-family:Consolas;">19-11-2024</font>

Tags: #cpp 

Related: NIL

****

## What are references?

References are unique to C++ and is used to create an alias to some variable. Modifying the value of the alias will also directly affect the original variable.

To create a reference variable, we declare it using `type &`.

For example, we can create an alias `b` for a variable `a`:

````cpp
int main(void)
{
    int a = 0;
    int &b = a;
	
    b++;
	
    cout << "a: " << a << endl; // a: 1
    cout << "b: " << b << endl; // b: 1
    return 0;
}
````

Incrementing the reference `b` by 1 also increases the value of `a` by 1. Since `a` and `b` are essentially the same variable, incrementing `a` will also affect `b`.


## References vs Pointers

References are very similar to pointers commonly used in C/C++. However, references are considered to be much safer than pointers since it prevents issues like dangling and `NULL` pointers.

While it it possible to declare a pointer variable pointing to nothing (dangling pointer), declaring a reference in the same way will lead to an error.

````cpp
int* ptr; // no errors, but will cause problems if used
int& ref; // raises an error
````

Declaring a reference as `NULL` will also raise an error while a pointer will not.

````cpp
int* ptr = NULL; // no errors
int& ref = NULL; // ERROR
````


## Referencing arrays

The same syntax could be used when declaring a reference to an array element. The code below creates an alias to the element at index 3 in the array `arr` and increments it's value by 10.

````cpp
int arr[] = {1, 2, 3, 4, 5};
int &element = arr[3];

element += 10;

cout << arr[3] << endl; // 14
````

If we want to create a reference to the entire array `arr` instead of just one element, the syntax for declaring the reference is slightly different.

````cpp
int arr[] = {1, 2, 3, 4, 5};
int (&arr_ref)[] = arr;

cout << arr_ref[2] << endl; // 3
````


## Pass by reference

Variables could be passed by reference to functions such that it could be modified directly within the function.

For example, a function `increment()` may create an alias of any input parameter so any modifications to the value within the function is also reflected outside the function.

````cpp
void increment(int& x){
	x++;
}

int main(void){
	int value = 10;
	increment(value);
	cout << value << endl; // 11
	return 0;
}
````


## Function returning reference

A function could also be crafted to return a reference to some variable.

For example, a function `max()` returns a reference to the largest element in an array.

````cpp
int &max(int arr[], int length)
{
    int max_index = 0;
    for(int i = 0; i < length; i++)
    {
        if(arr[i] > arr[max_index])
        {
            max_index = i;
        }
    }
    return arr[max_index];
}

int main(void)
{
    int a[] = {1, 2, 3, 4, 5, 14, 5, 2, 3, 2, 1};
    int size = sizeof(a) / sizeof(a[0]);
    int &max_element = max(a, size);
    cout << "Max element: " << max_element << endl;
    return 0;
}
````

If we modify the value of `max_element`, which is a reference to the element `14` in the array, the value `14` in the array will also be affected.

````cpp
max_element += 3;
cout << a[5] << endl; // 17
````


## Alias for functions

A reference could also be created for some function, following the syntax:

````cpp
return_type (&alias_name)(argument_type) = function;
````

For example, a function `foo()` accepts an integer `x` as the input argument and returns a float `x / 3`. To create an alias called `third` for the function, we write it as follows:

````cpp
float foo(int x)
{
	return (float)x / 3.0f;
}

// Create alias called Triple
float (&third)(int) = foo;
````

We can then call the function `foo` by it's alias:

````cpp
cout << third(9) << endl;
````