
Created <font style="color:tomato; font-family:Consolas;">12-10-2024</font>

Tags: #c

Related: [[Structures]]

****

## The [ -> ] operator 

How to we access the member variables of a struct from it's pointer? Instead of dereferencing it using the `*` operator and accessing the variable with the `.` operator, the arrow operator allows us to access it from the pointer directly.

````c
typedef struct
{
	int x;
	int y;
} Point;

int main()
{
	Point p1 = {3, 4};
	// create pointer to p1
	Point* pp1 = &p1; 
	// modify member from pointer
	pp1->x = 5; 
	pp1->y = 10;
	
	printf("%d, %d", p1.x, p1.y); // OUTPUT: 5, 10
	printf("%d, %d", pp1->x, pp1->y); // OUTPUT: 5, 10
	
	// create another pointer p2 in dynamic memory
	Point* p2;
	p2 = malloc(sizeof(Point));
	
	p2->x = 15;
	p2->y = 6;
	
	return 0;
}
````

If we decide that using the dereference operator `*` and `.` operator is better for some reason, keep in mind that we must include a bracket when dereferencing:

````c
typedef struct
{
	int x;
	int y;
} Point;

int main()
{
	Point p1 = {1, 2};
	Point* pp1 = &p1;
	(*pp1).x = 3; // This is fine
	*pp1.x = 3; // This is wrong
	
	return 0;
}
````
