
Created <font style="color:tomato; font-family:Consolas;">23-10-2024</font>

Tags: #c 

Related: [[CMD code execution]]

****

## Command line arguments handling

The `main()` function in C usually does not include any arguments, but we can pass it 2 special arguments called `argc` and `argv`.

````c
int main(int argc, char *argv[])
{
	return 0;
}
````

`argc` stands for arguments count, or the total number of arguments we pass the `main()` function in the command line.

`argv` stands for argument values, and is a character array of all the arguments passed to `main()` from the command line.

Consider the following code:

````c
#include <stdio.h>
int main(int argc, char * argv[])
{
	printf("arguments: %d\n", argc);
	
	while(argc--)
	{
		printf("arg[%d]: %c\n", argc, argv[argc]);
	}
	return 0;
}
````

If we simply executed the code in CMD using

````
gcc main.c -o main.exe
./filename.exe
````

The following will be printed in terminal:

````
arguments: 1
arg[0]: main.exe
````

When we run the code without any command line arguments, the default argument passed to `main()` is the file name. We have to take note that the file name is always the first argument in `argv`.

If we want to pass arguments to the program through the command line, we simply include it behind the file name, separating each argument with a space:

````
gcc main.c -o main.exe
main.exe aosdao 102398 ifiso pqworu
````

The output is now:

````
arguments: 5
arg[4]: aosdao
arg[3]: 102398
arg[2]: ifiso
arg[1]: pqworu
arg[0]: a.exe
````


## Converting string to `int`

All arguments passed to `main()` are strings. If we expect the argument to be an integer, we have to convert the string to an `int`. However, we cannot just use normal type-casting `(int)arg` since conversion from strings to integers are more complicated.

We have to use another standard library called `stdlib.h` for the `atoi` function, which converts strings to integers.

````c
#include <stdio.h>
#include <stdlib.h>
int main(int argc, char * argv[])
{
	printf("arguments: %d\n", argc);
	
	while(argc--)
	{
		int arg_int = atoi(argv[argc]); // convert to int
		printf("arg[%d]: %d\n", argc, arg_int);
	}
	return 0;
}
````

There are several other conversion functions available from `stdlib.h`, found [here](https://www.tutorialspoint.com/c_standard_library/stdlib_h.htm).

Some useful ones:

- `atof`: string to float
- `atoi`: string to int
- `atol`: string to long 