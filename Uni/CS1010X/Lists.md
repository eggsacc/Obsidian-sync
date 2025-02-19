
Created <font style="color:tomato; font-family:Consolas;">07-02-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## List vs tuples

Unlike tuples, lists in python are mutable, meaning that we can dynamically reassign values or adjust the size of the list.

Hence, unlike tuples, when we add more elements to a list by concatenating it with another list, the original list is mutated, not reallocated.

````python
list_a = [1, 2, 3]
tup_a = (1, 2, 3)

list_b = list_a
tup_b = tup_a

list_a += [4, 5]
tup_a += (4, 5)

print(list_b)             # [1, 2, 3, 4, 5]
print(tup_b)              # (1, 2, 3)
print(list_b == list_a)   # True
print(list_b is list_a)   # True
print(tup_a == tup_b)     # False
print(tup_a is tup_b)     # False
````


## List methods & functions

#### 1) `del`
Deletes an element from a list. E.g.: `del(a[0])`

#### 2) `len`
Counts the number of elements in a list. `len(list)`

#### 3) `min, max`
Returns the smallest / largest element in a list. `min(list), max(list)`

#### 4) `in`
Check if something is in a list. `1 in list`, `2 not in list`

#### 5) Multiplication behavior
If we multiplied a list by a constant, the list would be concatenated with multiple copies of itself.
`list * 2` gives `list + list`, not the elements inside times 2.

## List methods

#### 1) `.copy()`
Creates a copy of a list. The copies list has the same content but a different identity as the original.
`b = a.copy()`

#### 2) `.reverse()`
Reverses a list in-place. O(n) time complexity.

#### 3) `.insert(pos, val)`
Insert an element of value `val` at position `pos`.
`a.insert(0, 10)` inserts a 10 at position 0.

#### 4) `.pop(pos)`
Removes and returns an element from a list. If `pop` is used without an argument, it deletes and returns the last element. However, we can specify the index to `pop` at, such as `list.pop(2)` to delete and return the element at index 2.

#### 5) `.remove(val)`
Removes the first instance of the requested value from a list. `list.remove(2)` removes the first '2' in the list. If value is not found in list, Python will return an error.


