
Created <font style="color:tomato; font-family:Consolas;">17-03-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## What is a class?

A **class** in python is sort of like a blueprint for creating objects. It encapsulates both data and attributes that describes an object's behavior or state.

For example, we can have a class called `dog`. Within `dog`, there are some data fields like `height`, `weight`, `gender`, as well as attributes common to dogs like `bark()`, `whine()` etc. To create a new dog, we can simply create a new **instance** of the `dog` class; a new copy of all the data and attributes mentioned above will be associated with this new instance of `dog`.

## Classes in python

The structure of a class in python is as follows:

````python
class Class1(object):
	def __init__(self, init_x, init_y):
		self.x = init_x
		self.y = init_y
	
	def print_x(self):
		print(self.x)
	
	def inc_x(self, amt):
		self.x += amt
		return self.x
````

The first function, `def __init__(self, [params]):`, is known as the **constructor** function. This is the function that is called when a new instance of an object is first initialized. 

The `self` argument is a reference to the object calling the method. When passed as the first argument in any function, it basically gives the function or method access to all the variables and attributes of the reference object.

>[!tip] Double underscore notation
>The double underscores before and after the `__init__` method is a common notation known as "dunder names", and they are reserved for special predefined functions in Python.

### Creating an instance of the class

To create a new instance of `Class1`, we need to give it a unique name and provide the relevant constructor parameters.

````python
NewClass = Class1(1, 1)
````

The parameters `(1, 1)` are passed to the constructor as `init_x, init_y` respectively.


### Accessing class methods

The functions within a class are called **methods**, and they can be called using a dot operator `.` after a class instance.

````python
NewClass.print_x()
# Prints 1
NewClass.inc_x(10)
# Increments self.x by 10
````

