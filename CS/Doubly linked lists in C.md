
Created <font style="color:tomato; font-family:Consolas;">29-10-2024</font>

Tags: 

Related: NIL

****

## Creating a node

A node in a doubly linked list carries a pointer to both the previous and the next node.

````c
typedef struct Node
{
	int data;
	struct Node* prev_add;
	struct Node* next_add;
} Node;
````

Below is the function to create a new node and return it's address. The previous and next pointers are initialized to `NULL`.

````c
DNode* newNode(int data)
{
	Node* new_node = (Node*)malloc(sizeof(Node));
	new_node->data = data;
	new_node->prev_add = NULL;
	new_node->next_add = NULL;
	return new_node;
}
````


## Inserting nodes

> The functions written below is slightly different from those in [[Singly linked lists in C]]. Instead of having the functions return a pointer, the `head` and `tail` parameters are passed to the functions by reference and directly modified within the function itself.

### Insert at beginning

1) Create a new node
2) Check if the `head` address is `NULL` (empty list)
3) If list is empty, update `head` address to address of new node
4) Else, update links between new node and the previous `head`

````c
void insertNewAtStart(Node** head, int data)
{
    Node* new_node = newNode(data);
    
    if(*head != NULL)
    {
        new_node->next_add = *head;
        (*head)->prev_add = new_node;
    }
    else
    {
        *head = new_node;
    }
}
````


### Insert at end

1) Create a new node
2) Check if the list is empty (`tail == NULL`)
3) If empty, update `tail` address to be address of new node
4) Else, update links between `tail` and new node

````c
void insertNewAtEnd(Node** tail, int data)
{
    Node* new_node = newNode(data);
    
    if(*tail != NULL)
    {
        (*tail)->next_add = new_node;
        new_node->prev_add = (*tail);
    }
    else
    {
        *tail = new_node;
    }
}
````

### Insert at position

1) Check if requested position if valid (positive value)
2) Create a new node
3) Special case handling: insertion at beginning (position = 0)
4) Traverse list until 1 position before the requested position
5) If the node is `NULL`, raise an error and exit since the position is greater than the list length and `free` the new node created.
6) Else, check if the `pos - 1` node is the last node (points to `NULL`)
7) If true, the new node will be the new tail.
8) Else, update links to insert new node

````c
void insertNewAtPos(Node** head, int pos, int data)
{
    // error handling
    if(pos < 0)
    {
        printf("INVALID POS: NON-POSITIVE VALUE\n");
        return;
    }

    Node* new_node = newNode(data);
  
    // special case handling (insert at beginning)
    if(pos == 0)
    {
        if((*head) != NULL)
        {
            (*head)->prev_add = new_node;
            new_node->next_add = (*head);    
        }
        else
        {
            (*head) = new_node;
        }
        return;
    }

    Node* temp_ptr = (*head);
    int count = 0;

    for(int i = 0; i < pos - 1 && temp_ptr != NULL; i++)
    {
        temp_ptr = temp_ptr->next_add;
    }
    if(temp_ptr == NULL)
    {
        printf("INVALID POS: EXCEED LIST LENGTH\n");
        free(new_node);
        return;
    }

    temp_ptr->next_add = new_node;
    new_node->prev_add = temp_ptr;

    if(temp_ptr->next_add != NULL)
    {
        new_node->next_add = temp_ptr->next_add;
        temp_ptr->next_add->prev_add = new_node;
    }
    return;
}
````


## Deleting nodes

### Deleting node at start

1) Check if `head` is `NULL` (list is empty)
2) Check if the next node after original `head` exist (not `NULL`)
3) If it exist, set the next node as the new `head` 
4) If it does not exist, update the `head` to `NULL`
5) `free` the  original `head` address

````c
void delHead(Node** head)
{
    if((*head) == NULL)
    {
        return;
    }

    Node* new_head = (*head)->next_add;

    if(new_head != NULL)
    {
        new_head->prev_add = NULL;
        *head = new_head;
    }
    else
    {
        *head = NULL;
    }
    free(*head);
    return;
}
````

### Deleting node at end

1) Same procedure as deleting head node.

````c
void delTail(Node** tail)
{
    if((*tail) == NULL)
    {
        return;
    }

    Node* new_tail = (*tail)->prev_add;

    if(new_tail == NULL)
    {
        *tail = NULL;
    }
    else
    {
        new_tail->next_add = NULL;
        *tail = new_tail;
    }
    free(*tail);
    return;
}
````

### Delete from position

1) Check if requested position and head address is valid
2) Special case handling: delete first node
3) Traverse to one node before requested position
4) Check if next node exist
5) If exist, update links and `free` the node at requested position

````c
void delPos(Node** head, int pos)
{
    if((*head) == NULL || pos < 0)
    {
        printf("INVALID POS/HEAD");
        return;
    }

    if(pos == 0)
    {
        Node* new_head = (*head)->next_add;
        
        if(new_head != NULL)
        {
            new_head->prev_add = NULL;
            *head = new_head;
        }
        else
        {
            *head = NULL;
        }

        free(*head);
        return;
    }

    Node* temp_ptr = (*head);
    
    for(int i = 0; i < pos - 1 && temp_ptr != NULL; i++)
    {
        temp_ptr = temp_ptr->next_add;
    }

    if(temp_ptr == NULL || temp_ptr->next_add == NULL)
    {
        printf("INVALID POS: EXCEED LIST LENGTH");
        return;
    }

    temp_ptr->next_add = temp_ptr->next_add->next_add;
    temp_ptr->next_add->next_add->prev_add = temp_ptr;
    free(temp_ptr->next_add);
    return;
}
````
