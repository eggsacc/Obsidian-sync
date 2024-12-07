
Created <font style="color:tomato; font-family:Consolas;">13-10-2024</font>

Tags: #c #cpp 

Related: [[Structures]]

****

## Union vs structs

Similar to structs, unions are also used to define custom datatypes and can contain members of different types. However, the key difference between it and a struct is how memory is allocated and used.

A struct is always allocated enough memory to store all it's members, while a union is only allocated enough memory to store it's largest member. All members in a union share the same memory space in a overlapping manner.

#### Example

When we create a struct, the memory allocated is the sum of the size of all the members. Let's say we have a struct `Data`:

````c
struct Data
{
	int x;
	float y;
	char z[16];
}

int main()
{
	union Data data1
	printf("%d", sizeof(data1)); // OUTPUT: 24
	return 0;
}
````

An integer and float each occupies 4 bytes, and the character array of 16 elements is 16 bytes. Hence, $4+4+16=24$  bytes of memory is allocated to this struct to store all the members.

Now let's try to define `Data` as a union instead:

````c
union Data
{
	int x;
	float y;
	char z[16];
}

int main()
{
	union Data data1;
	printf("%d", sizeof(data1)); // OUTPUT: 16
	return 0;
}
````

As we can see, only 16 bytes of memory is allocated to `Data`, which corresponds to the size of it's largest member which is the 16 character array `z`.


## Shared memory space

Since the union is only allocated enough memory to store it's largest member, all the members share the same memory space and can overwrite each other.

In the code snippet below, a value is assigned to each member in the union.

````c
union Data
{
    int x;
    float y;
    char z[16];
};

int main() {

    union Data mydata;
    
    mydata.x = 10;
    mydata.y = 9.3;
    strcpy(mydata.z, "bro lol");
    
    return 0;
   }
````

If we use `printf` to check the value of each member, we get some garbage values for `x` and `y`.

````c
printf("mydata.x is: %d\n", mydata.x);
printf("mydata.y is: %f\n", mydata.y);
printf("mydata.z is: %s\n", mydata.z);
/* OUTPUT:
mydata.x is: 544174690
mydata.y is: 0.000000
mydata.z is: bro lol */
````

This illustrates how the members in a union "share" a memory space. When we assign a value to each of the members in sequence, it overwrites whatever was in the memory block before. In the example above, since the last set member was the character array `z`, members `x` and `y` has been overwritten. It is just translating part of the string "bro lol" into an integer and float when we try to access it using `mydata.x` and `mydata.y`.


## Pointers to unions

There are no differences between a pointer to a struct or union. The arrow operator ( -> ) and other features used for struct pointers applies to union pointers too.


## Uses

Unions are often used when we want to save storage space since it only allocated enough memory to store it's largest member. However, it could only be used if we are very certain that only 1 member will be modified / used at any time. We cannot use a union to store multiple variables at once since it will lead to data corruption.

#### Concatenating bits

One interesting use of unions is to concatenate bits into the full value.

````c
union BitField 
{ 
	struct 
	{ 
		unsigned int a : 4; // 4 bits for 'a' 
		unsigned int b : 4; // 4 bits for 'b' 
		unsigned int c : 8; // 8 bits for 'c' 
	} bits; 
	unsigned int value; // 16-bit value representing all bits together 
};
````


## Struct wrapper

Unions are often "wrapped" within structs so we can determine which member is active within the union to avoid accessing inactive member and getting garbage values.

In the code below, a struct is used to wrap a union `buffer`. We can set the `active` member in the struct to indicate that the first member of the union is currently being used, and implement a check to avoid accessing unused members.

````c
typedef struct
{
	int active;
	union
	{
		int x[32];
		float y[32];
		char z[64];
	} buffer;
} Data;

int main()
{
	Data data1;
	data1.active = 1;
	memset(data1.buffer.x, 26, 1024);
	
	if(data1.active == 1)
	{
		printf("%d", data1.buffer.x);
	}
	
	return 0;
}
````
