
Created <font style="color:tomato; font-family:Consolas;">23-10-2024</font>

Tags: 

Related: NIL

****

## Running code from terminal

### 1) Change directory

In command prompt, change the directory to where our `.c` file is stored:

````
cd C:\Users\yizha\Desktop\C
````

### 2) Compile program using GCC

The GNU Compiler Collection (GCC) is the most widely used C/C++ compiler today. It handles transforming our `.c` program file into a `.exe` machine code file that our computers understand. 

The GCC compiler handles the following processes:

1) **Pre-processing:** macro substitution for `#define`, and conditional compilation to include\exclude portions of code based on `#if #endif`. The input `.c` file is transformed into a `.i` file which is a "pure" C file without any pre-processor directives.

2) **Compilation:** generates assembly code by breaking down high-level C code to low-level operations. Transforms the `.i` input file into a `.s` assembly file.

3) **Assembly:** converts the barely human-readable assembly code to machine code (binary) specific to the CPU architecture. The input assembly `.s` file is transformed into a relocatable object file `.o` or `.obj`.

4) **Linking:** the gcc compiles all the object files into a single executable file. All the `#include` for external libraries are combined together to convert the bunch of `.obj` object files into a single, fully linked `.exe` executable file.

#### 2.1) Compiler options

To compile a program into an executable `.exe` file, we just have to run the command:

````
gcc file.c
````

However, GCC also has many command line arguments that could be provided to alter how the program is compiled. Some commonly used ones are listed below:

````
gcc file.c -c
````

The `-c` command skips the linking process during compilation. and the output will be the individual `.obj` or `.o` files.

````
gcc file.c -S
````

The `-S` command stops the compilation process at the assembling stage, producing a `.s` file written in assembly.

````
gcc file.c -E
````

The `-E` command stops the compilation at the pre-processor stage. The output file is simply the pre-processed source code.

````
gcc file.c -o new_filename
````

The `-o` command allows us to specify a new file name for the main output. 

````
gcc --help
````

Lists all the available commands and syntax.

### 3) Running the program

After compiling the program, a new `.exe` file will be produced in the same directory as the original program file. To run the program, we simply type in:

````
./filename.exe
````

and press enter.


### 4) Some CMD commands

Some commonly used CMD commands that could be useful:

````
cls
````

Clears command prompt.

````
ren oldname newname
````

Renames a file.

````
del filename
````

Deletes a file.

