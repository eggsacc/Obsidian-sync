
Created <font style="color:tomato; font-family:Consolas;">21-11-2024</font>

Tags: 

Related: NIL

****

## List comprehension

List comprehension is a way to create lists using a concise syntax to improve code readability. It is often used to replace traditional looping techniques.

For example, the traditional way of storing the square of all elements in a list it to iterate through the list using a `for` loop:

````python
a = [1, 3, 5, 7, 9]
squared = []
for element in a:
	squared.append(element ** 2)
````

This could be done in a single line using list comprehension:

````python
a = [1, 3, 5, 7, 9]
squared = [element ** 2 for element in a]
````


## Syntax

The general syntax for list comprehension is:

````python
[(expression) for item in iterable if (condition)]
````

The `if` condition is optional and could be applied to further filter the items to be added to the list.
