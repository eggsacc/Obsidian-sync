
Created <font style="color:tomato; font-family:Consolas;">01-10-2024</font>

Tags: #c #cpp #pointers 

Related: [[1) Introduction to pointers]] | [[2) Pointer arithmetic & types]] | [[3) Pointers to pointers]]

****

## Call by reference

We can call a function and pass the address of some variable to be de-referenced within the function.

````c++
void function(int* p){
	*p = (*p) + 1;
}

int main() {
	int a = 10;
	function(&a);
	cout << a;
}
````

In this code, we pass the **address** of the variable `a` into the function instead of `a` itself. The address is then de-referenced in the function to access the variable `a` and modify it's value. 

#### Benefits

If we passed a large / complex dataset into a function as an argument, it needs to create a copy of it for manipulation which could be taxing on program memory. Instead, we could just pass it by reference, and the function will be able to directly access and manipulate the data.

