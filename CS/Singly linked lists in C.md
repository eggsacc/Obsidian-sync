
Created <font style="color:tomato; font-family:Consolas;">28-10-2024</font>

Tags: #c #CS

Related: [[Linked lists]] | [[Arrow operator]] | [[Structures]]

****

## Creating a node

A node contains 2 elements: a data field and a pointer to the next node. A structure is used to represent each node.

> Note that since we are using the `Node` struct within the structure itself before it is fully defined, we have to explicitly declare `struct Node` within the `typedef` itself. 

````c
typedef struct Node
{
    int data;
    struct Node* next_add;
} Node;
````

To create a new node, we need a function to allocate memory to a new instance of the `Node` structure and initialize the pointer to `NULL`. The function will return a pointer to the location in heap memory the new node is created.

````c
Node* CreateNode(int val)
{
    Node* temp_ptr = (Node*)malloc(sizeof(Node));
    temp_ptr->data = val;
    temp_ptr->next_add = NULL;
    return temp_ptr;
}
````


## Inserting nodes

### Insert at beginning

Function arguments: head, value to initialize new node

1) Create a new node
2) If the list is empty, make this node the head and point to `NULL`
3) Else, new node points to current head
4) Return new node address to be set as new head

````c
Node* InsertNewNodeAtBeginning(Node* head, int value)
{
    Node* new_node = CreateNode(value);

    if(head == NULL)
    {
        return new_node;
    }
    
    new_node->next_add = head;
    return new_node;
}
````

### Insert at end

Function arguments: head, value to initialize new node

1) Create a new node
2) If list is empty, make this node the head and point to `NULL`
3) Else, traverse the entire list until the current last node
4) Update current last node to point to the new node

````c
Node* InsertNewNodeAtEnd(Node* head, int value)
{
    Node* new_node = CreateNode(value);
    
    if(head == NULL)
    {
        return new_node;
    }

    Node* current_ptr = head;
    
    while(current_ptr->next_add != NULL)
    {
        current_ptr = current_ptr->next_add;
    }

    current_ptr->next_add = new_node;
    return new_node;
}
````

### Insert at position

Function inputs: head, position to insert, value to initialize new node

1) Check if position is non-negative. Terminate function and return head address if false.
2) Special case handling: insertion at beginning (position == 0)
3) Create a new node
4) Traverse list until the `pos - 1` node.
5) If node points to `NULL`, position is invalid since it exceed list length
6) Else, insert new node in next position
7) Update new node to point to the next node, and the previous node to point to the new node

````c
Node* InsertNewNodeAtPosition(Node* head, int pos, int value)
{
    // error if negative pos
    if(pos < 0)
    {
        printf("INVALID POS: MUST BE NON_NEGATIVE\n");
        return head;
    }

    Node* new_node = CreateNode(value);
  
    // special case handling: insert at beginning
    if(pos == 0)
    {
        new_node->next_add = head;
        return new_node;
    }
  
    Node* temp_ptr = head;
    int count = 0;

    while(count < pos - 1 && temp_ptr != NULL)
    {
        temp_ptr = temp_ptr->next_add;
        count ++;
    }

    // error if pos > list length
    if(temp_ptr == NULL)
    {
        printf("INVALID POS: POS GREATER THAN LIST LENGTH\n");
        return head;
    }
  
    new_node->next_add = temp_ptr->next_add;
    temp_ptr->next_add = new_node;
    return head;
}
````


## Deleting nodes

The `free` function is used to deallocate memory from a node, thereby deleting it.
### Deleting first node

Function arguments: head

1) Check if head is `NULL` (empty list). If empty, return `NULL` and exit.
2) Store next node address as new head
3) Free current head address

````c
Node* DeleteFirstNode(Node* head)
{
    if(head == NULL)
    {
        return NULL;
    }
  
    Node* new_head = head->next_add;
    free(head);
    return new_head;
}
````

### Deleting last node

Function arguments: head

1) Exception handling: if list has no nodes, return `NULL` and exit. If list has only 1 node, free the node and return `NULL`.
2) Traverse list until the second last node
3) Free the last node
4) Update second last node to point to `NULL`.

````c
Node* DeleteLastNode(Node* head)
{
    if(head == NULL)
    {
        return NULL;
    }

    if(head->next_add == NULL)
    {
        free(head);
        return NULL;
    }
  
    Node* temp_ptr = head;
    while(temp_ptr->next_add->next_add != NULL)
    {
        temp_ptr = temp_ptr->next_add;
    }

    free(temp_ptr->next_add);
    temp_ptr->next_add = NULL;
    return head;
}
````

### Deleting node at position

Function arguments: head, position to delete

1) Exceptions handling: negative positions will return an error, while a list with only 1 node will have the head freed.
2) Traverse the list until 1 node before the requested position
3) Check if next node is `NULL` (position is out of list range), return error if true.
4) Store the node to be deleted (next node)
5) Update pointer to bypass stored node
6) Free the stored node.

````c
Node* DeleteNodeAtPos(Node* head, int pos)
{

    if(pos < 0 || head == NULL)
    {
        printf("INVALID POS: MUST BE NON-NEGATIVE\n");
        return NULL;
    }

    if(pos == 1)
    {
        Node* temp_ptr = head->next_add;
        free(head);
        return temp_ptr;
    }

    Node* temp_ptr = head;

    for(int i = 0; i < pos - 1; i++)
    {
        temp_ptr = temp_ptr->next_add;
    }
    
    if(temp_ptr == NULL || temp_ptr->next_add == NULL)
    {
        printf("INVALID POS: OUT OF LIST RANGE\n");
        return head;
    }

    temp_ptr->next_add = temp_ptr->next_add->next_add;
    free(temp_ptr->next_add);
    return head;
}
````


## Other operations

### Print list node values

Function arguments: head

1) Traverse list, printing the address, value and stored pointer of each node

````c
void TraverseList(Node* head)
{  
    Node* current_ptr = head;
    int count = 0;

    while(current_ptr != NULL)
    {
        printf("Node[%-2d] - address: %p, value: %-2d, next-address: %p\n",
                count, current_ptr, current_ptr->data, current_ptr->next_add);

        current_ptr = current_ptr->next_add;
        count++;
    }
}
````

### Search for node

Function argument: head, target value

1) Check through a list, returning true or false depending on if the requested data exists in a node.

````c
bool SearchForNode(Node* head, int target_value)
{
    Node* current_ptr = head;
    
    while(current_ptr != NULL)
    {
        if(current_ptr->data == target_value)
        {
            return true;
        }

        current_ptr = current_ptr->next_add;
    }
    return false;
}
````

### Search for node address

Function arguments: head, target value

1) Check through a list and return the address of the node that matches the requested value.
2) If value does not exist, return `NULL`

````c
Node* SearchForNodeAddress(Node* head, int target_value)
{
    Node* current_ptr = head;

    while(current_ptr != NULL)
    {
        if(current_ptr->data == target_value)
        {
            return current_ptr;
        }
        current_ptr = current_ptr->next_add;
    }
    return NULL;
}
````

### Find list length

Function arguments: head

1) Iterate through the list and count the total number of nodes.

````c
int FindListLength(Node* head)
{
    Node* current_ptr = head;
    int length = 0;
    
    while(current_ptr != NULL)
    {
        length ++;
        current_ptr = current_ptr->next_add;
    }
    return length;
}
````