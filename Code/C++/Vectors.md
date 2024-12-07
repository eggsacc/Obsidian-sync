
Created <font style="color:tomato; font-family:Consolas;">19-11-2024</font>

Tags: #cpp 

Related: NIL

****

## Dynamic arrays

In low-level languages like C, arrays have a fixed size that is determined at compile time. This means that we cannot add more elements to an existing array without reallocating memory, and the lack of automatic garbage collection also means that removing elements does not decrease the size of the array.

C++ introduces a type of dynamic array called `std::vectors`, which can resize itself automatically when elements are inserted or deleted. It is included in the `vector` standard library.

````
#include<vector>
````


## Syntax

To declare a vector variable, we need to define it's type and give it a name.

````cpp
vector<type> name;
````

It can also be initialized in different ways:

````cpp
// Initialize to specific size and
// default value
vector<type> v1(size, default_value);

// Using initialization list
vector<type> v1 = {1, 2, 3, 4};
vector<type> v1({1, 2, 3, 4});
````


## Accessing elements

Like arrays, elements in vectors can be accessed by their index using the subscript operator `[]`. However, just like arrays, we can totally access indexes out of the vector's range. There is a method `.at(index)` which could be used to safely access the elements with automatic bounds checking.

````cpp
vector<int> vec1 = {1, 2, 3, 4};

cout << vec1[2] << endl;
cout << vec1.at(2) << endl;

cout << vec1[4] << endl; // no error; prints garbage value
cout << vec1.at(4) << endl; // throws an out of range error
````


## Updating elements

Same as changing the values in arrays. The `.at()` method could also be used.

````cpp
vec[1] = 3;
vec.at(1) = 3;
````


## Traversing vector

The `.size()` method returns the number of elements in the vector. A `for` loop could be used to iterate through the elements.

````cpp
for(int i = 0; i < vec1.size(); i++)
{
	cout << vec[i] << endl;
}
````

Alternatively, use a [[Range-based loop]].

````cpp
for(auto n : vec1)
{
	cout << n << endl;
}
````


## Inserting elements

To append a new element to the end of the vector, use `.push_back(value)`. This process takes constant time.

````cpp
vec1.push_back(5);
````

To insert an element at any position, use `.insert(position, value)`. This process takes linear time. Note that the position for insertion is specified by an iterator, not the index.

````cpp
vec1.insert(vec1.begin() + 1, 3); // insert 3 at index 1
````


## Deleting elements

The last element in the vector can be removed using `.pop_back()`. This process takes constant time.

````cpp
vec1.pop_back(); // removes last element
````

To remove an element at other positions, we can use `.erase(position)`. Again, the position argument has to be an iterator to the element. If we want to remove an element of a specific value without knowing it's position, we have to use `find()` to locate it's iterator first.

````cpp
vec1.erase(vec1.begin() + 2); // deleted element at index 2

// locate element of value 3, then delete it
vec1.erase(find(vec1.begin(), vec1.end(), 3));
````


## Capacity

Vectors will automatically resize itself when we add or remove elements by adjusting it's capacity, but the capacity is not equal to the number of elements. We can check the capacity of a vector (amount of memory allocated) using the `.capacity()` method.

````cpp
cout << vec1.capacity() << endl;
````

If we check the capacity of a bunch of vectors with different number of elements, we realize that is actually doubles it's capacity every time the number of elements exceed the current capacity.

````
Number of elements: 4
Capacity: 6

Number of elements: 7
Capacity: 12

Number of elements: 13
Capacity: 24

Number of elements: 25
Capacity: 48
````

Vectors simply double in capacity when the number of elements exceed it's current capacity. This is because re-allocating memory is time consuming, so it rather allocate an excess of memory to accommodate future additions than resize to match the number of elements every time the size changes.

## Summary of common functions

````cpp
vector<int> v;
// ==============================================
// returns number of elements
v.size();
// returns max number of elements vector can hold
v.max_size();
// returns amount of memory allocated
v.capacity();
// ==============================================
// access element at position
v.at(pos);
// access first element
v.front();
// access last element
v.back();
// returns iterator pointing to first element
v.begin();
// returns iterator pointing to last element
v.end();
// ==============================================
// add element to the end
v.push_back(value);
// add element to end directly (more efficient)
v.emplace(value);
// insert element at position
v.insert(v.begin() + pos, value);
// insert element at position directly (more efficient)
v.emplace(v.begin() + pos, value);
// ==============================================
// delete last element
v.pop_back();
// delete element at position
v.erase(v.begin + pos);
// delete all elements
v.clear();

````