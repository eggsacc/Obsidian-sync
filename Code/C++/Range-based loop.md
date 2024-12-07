
Created <font style="color:tomato; font-family:Consolas;">18-11-2024</font>

Tags: #cpp

Related: NIL

****

## Iterating through arrays

The standard way of iterating through an array is as follows:

````cpp
int main(void)
{
	int arr[] = {1, 2, 3, 4, 5};
	int arr_size = std::size(arr);
	
	for(int i = 0; i < arr_size; i++)
	{
		std::cout << arr[i] << endl;
	}
	return 0;
}
````

A **range-based loop** is a more readable syntax used to execute a `for` loop over a range. It has the following syntax:

````cpp
for (range_declaration : range_expression) {loop_statement}
````

Following the previous example, we can iterate through the array like so:

````cpp
int arr[] = {1, 2, 3, 4, 5};

for(auto n : arr)
{
	std::cout << n << endl;
}
````

The initializer could also be a braced list:

````cpp
for(auto n : {1, 2, 3, 4, 5})
{
	std::cout << n << endl;
}
````

