
Created <font style="color:tomato; font-family:Consolas;">25-10-2024</font>

Tags: #CS

Related: NIL

****

## What are linked lists?

A singly linked list is a fundamental data structure in computer science and programming. It consists of nodes which contains a **data field** and a **reference** to the next node in line. The last node points to `NULL`, indicating the end of the list. Insertion and deletion operations are very efficient in linked lists, but searching and indexing a linked list could prove more cumbersome.

## Linked lists vs arrays

Both arrays and linked lists stores a set of data. The key difference is how the data is stored in memory and how it is being accessed.

## Arrays
**** 
### Memory allocation

When we initialize an array with 5 elements, like `int array[5] = {1, 2, 3, 4, 5}`, a contiguous block of memory is allocated to the array.

![[Pasted image 20241025090222.png#invert]]

To access the elements in the array, we only need to know the address of the first element, i.e. `1000` following the example above.

Since all the element are of the type `int`, which occupies 4 bytes of memory, the address of each element can be found by jumping 4 addresses from the starting address.

Arrays are usually stored on the stack since it has a fixed size. Hence, indexing arrays are much faster since accessing stack memory is generally more efficient.

### Indexing / searching

Arrays are easily indexed since all the elements are of the same type and share a contiguous block of memory. Given the starting address of the array `arr`, the address of the $n^{th}$ element could be easily calculated by:

$$
address = n\times sizeof(element)+ arr
$$

Since the address of any element is easily determined, searching or accessing elements in an array is very efficient.

### Dynamic size

When elements are deleted from an array, the extra empty memory locations are actually still reserved for the array, meaning that other processes cannot utilize this memory unless the entire array is deallocated from memory. Hence, arrays require careful memory management and manual garbage collection.

Resizing arrays are quite troublesome since the computer needs to allocate a new, different size of contiguous memory and copy all the elements over.

### Insertion/ deletion

Inserting/deleting elements from the end of an array is simple since we can do it without affecting other elements.

![[Pasted image 20241025131710.png#invert]]

However, if we want to add or remove elements in other places, we have to shift all the subsequent elements up or down by 1 position.

For example, inserting a new element at index `[1]` requires the following 4 elements to be shifted 1 position up to make space for it.

![[Pasted image 20241025133222.png#invert]]

If we had a large array with say 10,000 elements, inserting or deleting a new element at position [2] will require 9998 elements to be shifted. The computer can only shift 1 element at a time, hence it is quite inefficient to add/remove elements from large arrays.

For arrays, insertion/deletion at the end is constant time $O(1)$ complexity, while inserting or deleting elements at any other position takes linear time $O(n)$.

## Linked lists
**** 
### Memory allocation

Unlike arrays, nodes in a linked list do not share a block of contiguous memory. Each node not only contains the data, but also the address of the next node in line. The last node will point to `NULL`, indicating the end of the list.

![[Pasted image 20241025092601.png#invert]]

Similar to arrays, we only need the address of the first node (commonly referred to as the **head**) to find any other node by traversing through the list.

Linked lists nodes costs slightly more memory to store since it also contains an extra pointer variable.

Since nodes are allocated on the heap, accessing nodes are slower than accessing arrays elements. Accessing nodes also costs more overhead since custom functions are usually written to manage linked lists.

### Indexing / searching

Since the nodes in a linked list are non-contiguous in memory, we have to always start from the first node (head) and slowly work our way up to the $n^{th}$ node to find it's address. It is impossible to determine where a node is located without first locating the previous node.

Hence, indexing singly linked lists are often quite troublesome since we have to search from the head of the list every single time.

### Dynamic size

Linked lists are much more flexible than arrays since the nodes are non-contiguous in memory.

Expanding a linked list simply creates more nodes at random locations in memory, while shrinking the list simply frees the memory of the deleted nodes. There is no risk of unused, allocated space like arrays.

### Insertion/deletion

Linked lists feature constant time $O(1)$ insertion and deletion at any position in the list. When we add or remove a node anywhere in the linked list, we just have to update the address where the previous node and new node points to.

![[Pasted image 20241025135533.png#invert]]

In the example above, after inserting a new node between `Node1` and `Node2`, we only have to update the address `Node1` and the new `Node2` points to. `Node1` will now point to the address of the newly inserted `Node2` which is at `1207`, and the new node will point to the the address of  the next node `Node3` which is at `1008`.

Hence, adding or removing nodes in a linked list is a constant time $O(1)$ operation if we know the address of the preceding node, or when inserting at the beginning of the list. If we do not know the address to insert the new node at, the process takes linear time $O(n)$ since we have to traverse the list from the start until we find the address for insertion.

