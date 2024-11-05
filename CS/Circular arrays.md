
Created <font style="color:tomato; font-family:Consolas;">02-11-2024</font>

Tags: #CS 

Related: NIL

****

## What are circular arrays?

Circular arrays have it's elements arranged in a circle - the next index after the last element is the first element. 

![[Pasted image 20241102144149.png#invert]]

In a circular array, it is possible to find the element at a position exceeding the array length since it just wraps around and starts from the beginning. The modulo operator `%` can be used to find the index of an element at any position.

````c
int index = position % array_length
````

For example, the element at position `8` in an array of 5 elements is the same as the element at position 3 since $8\%5=3$.

````c
int findElement(int* arr, int length, int pos)
{
	return arr[pos%length];
}
int array[5] = {1, 3, 5, 7, 9};

int element = findElement(array, 5, 8);
printf("%d\n", element); // 5
````

