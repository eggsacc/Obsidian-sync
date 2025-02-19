
Created <font style="color:tomato; font-family:Consolas;">07-02-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## Recursively count items in tree

````python
def count_leaves(tree):
	# If tree empty, return 0
	if(tree == ()):
		return 0
	# If current element is not a tuple, return 1 (already root value)
	elif(type(tree) != tuple):
		return 1
	# Else: is a tuple -> explore furthur
	else:
		return count_leaves(tree[0]) + count_leaves(tree[1:])
````


## Flatten a tree

````python
def flatten_tree(tree):
	if(tree == ()):
		return ()
	elif(type(tree) != tuple):
		return (tree,)
	else:
		return flatten_tree(tree[0]) + flatten_tree(tree[1:])
````

## Applying function to each element in tree

If we want to apply a function to each element in a tree, but still keep the original structure (without flattening it), we can use the `map` function.

````python
def map_tree(tree, factor):
	# Function to multiple leave with factor
	def scale_fn(subtree):
		if(type(subtree) != tuple):
			return factor * subtree
		else:
			return map_tree(subtree, factor)
	return map(scale_fn, tree)
````


## Iterative approach to counting leaves

````python
def count_leaves(tree):
    stack = tree
    count = 0

    while(stack != ()):
        print(f"stack: {stack}")
        if(type(stack[0]) != tuple):
            count += 1
            stack = stack[1:]
        else:
            tup = stack[0]
            stack = stack[1:]

            for item in tup[-1::-1]:
                stack = (item,) + stack
    return count
````
