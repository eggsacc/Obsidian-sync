
Created <font style="color:tomato; font-family:Consolas;">29-10-2024</font>

Tags: #CS 

Related: [[Linked lists]]

****

## Structure

In a doubly linked list, each node carries a pointer to both the next node and the previous node.

![[Pasted image 20241029071939.png]]

The first and last addresses are `NULL` to indicate the start and end of a list.

Since each node knows where the previous and next node is located, we can traverse the list in **both directions**, either from the **head** (first node) or the **tail** (last node). Insertion of nodes in intermediate positions are also more efficient than singly linked lists since both the previous and next addresses can be determined within the same node.
