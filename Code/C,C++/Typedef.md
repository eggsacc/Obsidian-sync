
Created <font style="color:tomato; font-family:Consolas;">11-10-2024</font>

Tags: #c #cpp 

Related: [[Structures]]

****

## What are typdefs?

`typedef` is a reserved keyword in C that gives an existing datatype a "nickname", making it easier to reuse and understand.

#### Example

````c
const volatile uint32_t a = 10;
const volatile uint32_t b = 10;
const volatile uint32_t c = 10;
````

Instead of writing this lengthy datatype repeatedly, we can use a typedef to give it a "nickname":

````c
typedef const volatile uint32_t cvint;

cvint a = 10;
cvint b = 10;
cvint c = 10;
````


## Typedef & structs

We can use typedef on structs to make them a little more convenient to setup.

````c
typedef struct
{
	char name[20];
	int age;
	char password[20];
} User;

int main()
{
	User user1 = {"Gay", 25, "password123"};
	User user2 = {"Gayer", 39, "P@ssword1"};
}
````
