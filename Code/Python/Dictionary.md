
Created <font style="color:tomato; font-family:Consolas;">19-11-2024</font>

Tags: #python

Related: NIL

****

## Dictionary

A **dictionary** is Python's implementation of a hash map. It stores key-value pairs, and features constant-time look-up regardless of the dictionary size.

To initialize a dictionary, we use a set of curly braces:

````python
dictionary = {}
````

We can also convert any iterable object to a dictionary using `dict()`.

````python
num_list = [[1, 3], [5, 2], [8, 4]]
dictionary = dict(num_list)
````

## Operations

### Initialization

Key-value pairs could be assigned on initialization:

````python
dictionary = {"alpha" : 1, "beta" : 2}
````

### Adding key-value pair

To add a key-value pair, we use the syntax:

````python
dictionary[key] = value

# Example:
dictionary["charlie"] = 3
````

### Accessing elements

To access an element:

````python
print(dictionary["alpha"]) # 1
````

There is also a method `get()` to access elements. This is a safer way of accessing elements since the method prevents errors when requesting for a non-existent key.

````python
print(dictionary.get("alpha")) # 1
````

The `.items()` method returns a list of tuples representing each key-value pair.

````python
print(dictionary.items())
# [("alpha", 1), ("beta", 2), ("charlir", 3)]
````

The `.keys()` method returns a list of all the keys.

````python
print(dictionary.keys())
# ["alpha", "beta", "charlie"]
````

The `.value()` method returns a list of all the associated values.

````python
print(dictionary.values())
# [1, 2, 3]
````

The `.has_key()` method checks for the presence of a key.

````python
dictionary.has_key("delta") # False
````


### Deleting keys

To remove an element from the dictionary, use the keyword `del`.

````python
del(dictionary[beta])
````

Another way of doing so is to use the method `.pop()`.

````python
dictionary.pop("charlie")
````

To remove the last-inserted key-value pair, use `.popItem()`.

````python
dictionary.popItem()
````

To delete all elements, use the method `.clear()`.

````python
dictionary.clear()
````


### Other methods

The `all()` function checks if all values in the dictionary evaluates to `True`.

````python
my_dict = {‘A’: 10, ‘B’: 20, ‘C’: 0}  
print(all(my_dict.values())) # False since "C" is 0 -> False
````

The `any()` function returns `True` if at least 1 of the elements evaluates to `True`.

````python
my_dict = {‘A’: 10, ‘B’: 20, ‘C’: 0}  
print(any(my_dict.values())) # True since "A", "B" evaluate to True
````

