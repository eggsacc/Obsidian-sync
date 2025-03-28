
Created <font style="color:tomato; font-family:Consolas;">19-03-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## What is a priority queue?

A priority queue is a type of abstract data structure that allows elements of higher priority to be retrieved before those of lower priority at constant time by always placing the highest priority element either at the start or end of the queue.

There are many ways to implement a priority queue, and we will be glossing over some implementations like sorted/unsorted arrays and focus on a min heap instead.

### Brief implementation using sorted array

One of the simplest methods to implement a priority queue is the use of a sorted array. 

A sorted array consists of elements that are strictly increasing or decreasing. Hence, the highest priority element could be retrieved from either the front or back of the array with ease.

![[Pasted image 20250319133703.png#invert]]

Inserting a new element into the priority queue requires us to iterate through array to find the correct insertion position. Obtaining the highest priority element is easy since all we have to do is look at the first element.

### Priority queue uses

One use case of priority queues is in CPU task scheduling, where the processor has to pick and execute the task with the highest priority every time. New tasks are pushed into the priority queue, and the highest priority task will always appear at the top of the queue, enabling the processor to quickly decide on the next task to execute.


## Min heap

As previously mentioned, sorted lists could be used to implement a priority queue. However, it is far from being the ideal implementation due to the inefficient insertion procedure. 

In a sorted list, we need to iterate through the list to find the correct position to insert a new element. However, a priority queue only requires the smallest element to be at the top, and the sequence of the rest of the elements actually does not matter.

This is where a **min heap** data structure comes in handy. A min heap data structure is a type of binary tree structure where the value of each parent node is smaller than the value of it's 2 children nodes.


![[Pasted image 20250319134911.png#invert]]

The above min-heap tree structure could be flattened and represented by an array:

![[Pasted image 20250319134928.png#invert]]

Notice that in the array, the index of each child node could be related to their parent node by the following equation:

$$
p=(i-1) //2
$$

Where `i` is the index of a child node.

For example, for the value `7` at index `4`, it's parent node would be located at index $(4-1)//2 = 1$. And we can see that is indeed the case since `3` (index `1`) is the parent node of `7`.

### Insertion in min heap

The process to insert a new element into a min heap is as such:

1) Append the element to the end of the array
2) Repeatedly compare the element to it's parent node. If it is smaller than the parent node, swap it's position with the parent node.

Let's say we want to insert the value `2` into the min heap above. The process would look something like this:


![[Pasted image 20250319143405.png#invert]]



![[Pasted image 20250319143649.png#invert]]

Note that in a min heap structure, only the smallest element is at the top. The remaining layers only obey the rule that the parent node is smaller than both it's children nodes. It is not the same as a sorted binary tree, where each individual layer is sorted.

The main advantage of using a binary min heap is that inserting any element now takes at most $O(\log n)$ time instead of $O(n)$ time, while retrieval of the highest priority (smallest) element is still $O(1)$ time. 


## Deleting from min heap

The min heap is mostly set up, but we are still missing the crucial functionality to delete nodes from the min heap.

For example, if we want to remove and use the smallest element at the top, how do we ensure that the next smallest element replaces it's spot?

The process of removing a node in a min heap follows the below structure:

1) After removing a node, move the last node to fill it's position.
2) Starting from the position of the removed node, keep checking if the node value against the 2 children nodes. Swap it's position with the smallest children node if a children node is smaller.
3) Repeat until no further swaps are required; the tree should now be in the correct order again.

Consider the following min heap:


![[Pasted image 20250319192122.png#invert]]


If we want to delete the smallest node, `1`, the process would look something like this:


![[Pasted image 20250319192922.png#invert]]

![[Pasted image 20250319192954.png#invert]]

Considering the array representation of a min heap, the index of the left and right children given the parent node index is:

$$
\begin{align}
\text{left}=i*2+1 \\  \\
\text{right}=i*2+2
\end{align}

$$

