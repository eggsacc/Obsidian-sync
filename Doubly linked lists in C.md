
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


