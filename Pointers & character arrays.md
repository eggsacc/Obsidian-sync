
Created <font style="color:tomato; font-family:Consolas;">02-10-2024</font>

Tags: #c #cpp #pointers

Related: [[Introduction to pointers]] | [[Pointer arithmetic & types]] | [[Pointers & arrays]]

****

# Storing characters

Let's say we want to store the characters in the word "GAY" in an array and print the array out.

````c++
int main()
{
    char arr[] = {'G', 'A', 'Y'};
    cout << arr << endl;
    
    return 0;
}
````

We would expect it to just print `GAY`. However, the output is `GAYτ«]ñ☺`. Where did those random characters come from?

In C/C++, character arrays are treated as strings when passed into functions like `printf` or `cout`. A string is a null terminated sequence of characters, meaning it ends with the special character `\0`, also known as the null character. In the code above, there is no `\0` in the array, so the `cout` function just keeps reading memory past the array and printing random characters.

When creating an array to store $n$ characters, the array size has to be $n+1$. We will always need to store the null character `\0` at the end to indicate that there is no more valid characters pass this point.

After adding the `\0`, the code works as expected.

````c++
int main()
{
    char arr[] = {'G', 'A', 'Y', '\0'};
    cout << arr << endl;
    
    return 0;
}

// OUTPUT: GAY
````


## String literals

The `<string.h>` library that comes bundled with C/C++ introduces string literals, which is an easier way of declaring character arrays.

After we `#include<string.h>` in our program, we can use string literals to store characters instead of arrays.

````c++
// storing in arrays
char characters[] = {'A', 'B', 'C', 'D', '\0'};

// using string literals
char characters[] = "ABCD";

// Wrong way to declare string literals. returns error
char characters[];
characters = "ABCD";
````

Some points to note when using string literals:

1) Double quotation marks `""` must be used, not interchangeable with `''`. 
2) String literals are implicitly null terminated, meaning it is automatically stored with a `\0` included.
3) String literals must be declared in one line. See example above.

#### The `lenstr()` function

There is a function `strlen()` that returns the number of characters in a string, excluding the null terminator. It is different from the `sizeof()` function which returns the size of the array inclusive of the null terminator.

````c++
main()
{
    char characters[] = "ABCD";

    cout << strlen(characters) << endl; // OUTPUT: 4

    cout << sizeof(characters) << endl; // OUTPUT: 5

    return 0;
}
````


## Pointers to character arrays

Pointers work the same for character arrays as other types of arrays. The name of a character array is still the address of it's first element.

````c++
char random_word = "HEHEHEHA";

// both lines of code below does the same thing
// since random_word is already referring to the address.
char* ptr = random_word;
char* ptr = &random_word;

// modifying value of element
ptr[1] = 'M'; // change element 1 to M: HMHEHEHA
*(ptr + 2) = "P"; // change element 2 to P: HEPEHEHA
````

Arrays in general are always passed by reference to functions. Character arrays are no exception.


## String constants

When we initialize any array using a string literal, the string gets stored as an array in the stack, and we can freely modify it's contents. However, when we use a string literal elsewhere without initializing an array, it gets stored as a compile time constant and is unmodifiable. 

````c++
// initializing array using string literal
char arr[] = "ABCDEFG";
arr[0] = 'F'; // change element 0 to 'F' instead of 'A'

// without initializing array
char* string_ptr = "ABCDEFG";
string_ptr[0] = 'F'; // error: could not modify, access violation
````


## Read-only string

Sometimes we don't want a function to modify the contents of a string array passed to it. The `const` keyword is used before the array pointer to make it immutable.

````c++
void onlyReadString(const char* arr)
{
	arr[0] = 'A'; // error: unable to write to address
}
````
