
Created <font style="color:tomato; font-family:Consolas;">11-10-2024</font>

Tags: #c #cpp 

Related: [[Typedef]]

****

## What are structs?

Structures, more commonly referred to as just structs, is a collection of related members or variables in C/C++. Similar to arrays, the variables in a struct are listed under one name in a memory block, but the variables could be of different data types.

Structs are often used to define a custom datatype. 

#### Defining a struct

````c
struct Name
{
	// members;
};
````

#### Example

If we decide that a classroom can be defined by it's name, number of tables, chairs, arrangement, and cleanliness, we can group all those characteristics in a struct:

````c
struct Classroom
{
	char name[2];
	int tables;
	int chairs;
	int arrangement[2];
	float cleaniness;
}
````


## Using structs

We can initialize variables using the struct, and access or modify the members in the variables using a dot.

````c
struct Classroom
{
	char name[2];
	int tables;
	int chairs;
	int arrangement[2];
	float cleaniness;
}

int main()
{
	struct Classroom Class1;
	struct Classroom Class2;
	
	strcpy(Class1.name, "A1");
	Class1.tables = 20;
	Class1.chairs = 23;
	Class1.arrangement[0] = 4;
	Class1.arrangement[1] = 5;
	Class1.cleaniness = 9.67;
	
	printf("%d", Class1.tables);
	return 0;
}
````

Note that we can only modify a character array stored in a struct using the method `strcpy()`. Any other datatypes can be directly accessed and modified with `struct.member`.

We can also assign values to the members when initializing the struct variable:

````c
struct Classroom Class2 = {"B2", 19, 19, {4, 4}, 6.96}; // order matters!
````

In the above syntax, the order or which the members are initialized matters. The members are assigned values from top to bottom.

Another way of initializing variables is:

````c
struct Classroom Class2 = 
	{
	.tables = 10, 
	.chairs = 19, 
	.cleaniness = 8.78
	};
````

In this case, we specify which member the values are assigned to, so the order does not matter.

## Typedef struct

Structs are often used with typedef to simplify the syntax.

````c
typedef struct
{
	char name[2];
	int tables;
	float cleaniness;
} Classroom;              // name of struct moved here

int main()
{
	Classroom Class2 = {"B2", 10, 9.32};
	return 0;
}
````


## Structs as function arguments

Since a struct is basically a custom datatype, we can pass it to functions like any other variable.

````c
typedef struct
{
	char name[2];
	int tables;
	float cleaniness;
} Classroom;              // name of struct moved here

void PrintClassCharacteristics(Classroom Class)
{
	printf("%s", Class.name);
	printf("%d", Class.tables);
	printf("%d", Class.cleaniness);
}

int main()
{
	Classroom Class2 = {"B2", 10, 9.32};
	PrintClassCharacteristics(Class2);
	return 0;
}
````


## Structs as return type

We can use a struct to define a function return type.

````c
typedef struct
{
	char name[2];
	int tables;
	float cleaniness;
} Classroom; 

Classroom CreateClass(char name[], int tables, float cleaniness)
{
	Classroom class1;
	class1.name = name;
	class1.tables = tables;
	class1.cleaniness = cleaniness;
	
	return class1;
}
````
## Assignment

If we have 2 structs `S1` `S2` and we make `S1` = `S2`, `S1` will copy over everything from the memory block of `S2`.

````c
typedef struct
{
	int x;
	int y;
} Point;

int main()
{
	Point S1 = {10, 12};
	Point S2 = {2, 4};
	S1 = S2;
	printf("%d", S1.x); // OUTPUT: 2 
	return 0;
}
````


## Array of structs

We can create an array of a struct.

````c
typedef struct
{
	int x;
	int y;
} Point;

int main()
{
	Point points[5];
	
	for(int i = 0; i < 5; i++)
	{
		points[i].x = i;
		points[i].y = 5 - i;
	}
	
	// or
	Point points[5] = {{1, 2}, {2, 4}, {1, 3}, {5, 7}, {7, 3}};
	
	// or
	Point points[3] = {
		{1, 4},
		{2, 5},
		{6, 7}
	};
	
	return 0;
}
````

An array of structs has similar characteristics to conventional arrays when passed as function arguments, i.e. it also gets passed as a pointer but of a custom datatype.

The following methods of initializing function parameters are all accepted.

````c
void SomeFunc(Point points[5]){}
void SomeFunc(Point points[]){}
void SomeFunc(Point *points){}
````


## Structs in structs

Since a struct is a custom datatype, we can create structs with members whose type is also a struct.

````c
typedef struct
{
	int x;
	int y;
} coordinates;

typedef struct
{
	char cityName[20];
	int population;
	coordinates location; // member variable "location" of type "coordinates"
} City;

int main()
{
	City cities[2] = {
		{"Singapore", 50000, {1256, 2948}},
		{"USA", 100000, {593, 6828}}
	};
	
	ptinf("City: %s, x-coord: %d", cities[0].cityName, cities[0].location.x);
	ptinf("City: %s, y-coord: %d", cities[1].cityName, cities[1].location.y);
	
	return 0;
}
````
