# Elementary Data Structures: Linked Lists

## Introduction to Linked Lists
A linked list is a linear data structure consisting of nodes that hold data and pointers to other nodes. Unlike arrays, linked lists do **not require contiguous memory allocation**, making them dynamic and flexible for insertions/deletions. Each node contains:
- **Data**: The value stored in the node
- **Pointer(s)**: Reference(s) to adjacent nodes

---

## Types of Linked Lists
1. **Singly Linked List**:  
   Each node points to the next node.  
   `[Data | Next] → [Data | Next] → ... → NULL`

2. **Doubly Linked List**:  
   Each node points to both the next and previous nodes.  
   `NULL ⇄ [Data | Prev | Next] ⇄ [Data | Prev | Next] ⇄ NULL`

3. **Circular Linked List**:  
   The last node points back to the first node.  
   `[Data | Next] → [Data | Next] → ... → Head`

---

## Basic Operations
| **Operation**       | **Description**                                  | **Time Complexity** |
|----------------------|--------------------------------------------------|---------------------|
| **Insertion**        | Add a node at the beginning, end, or position    | O(1) or O(n)        |
| **Deletion**         | Remove a node from any position                  | O(1) or O(n)        |
| **Traversal**        | Visit every node sequentially                    | O(n)                |
| **Search**           | Find a node by value                             | O(n)                |
| **Update**           | Modify the data of a node                        | O(n)                |

---

## Implementations in C

### 1. **Singly Linked List**
```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *next;
} Node;

// Create a new node
Node* createNode(int value) {
    Node *newNode = (Node*)malloc(sizeof(Node));
    if (!newNode) {
        printf("Memory error\n");
        exit(1);
    }
    newNode->data = value;
    newNode->next = NULL;
    return newNode;
}

// Insert at the beginning
void insertAtBeginning(Node **head, int value) {
    Node *newNode = createNode(value);
    newNode->next = *head;
    *head = newNode;
}

// Insert at the end
void insertAtEnd(Node **head, int value) {
    Node *newNode = createNode(value);
    if (*head == NULL) {
        *head = newNode;
        return;
    }
    Node *temp = *head;
    while (temp->next != NULL) {
        temp = temp->next;
    }
    temp->next = newNode;
}

// Delete a node by value
void deleteNode(Node **head, int value) {
    if (*head == NULL) {
        printf("List is empty\n");
        return;
    }
    Node *temp = *head, *prev = NULL;
    
    // If head node holds the value
    if (temp != NULL && temp->data == value) {
        *head = temp->next;
        free(temp);
        return;
    }
    
    // Search for the node
    while (temp != NULL && temp->data != value) {
        prev = temp;
        temp = temp->next;
    }
    
    if (temp == NULL) {
        printf("Value not found\n");
        return;
    }
    
    prev->next = temp->next;
    free(temp);
}

// Print the list
void printList(Node *head) {
    Node *temp = head;
    while (temp != NULL) {
        printf("%d → ", temp->data);
        temp = temp->next;
    }
    printf("NULL\n");
}

int main() {
    Node *head = NULL;
    
    insertAtEnd(&head, 10);
    insertAtBeginning(&head, 5);
    insertAtEnd(&head, 20);
    
    printf("Original List: ");
    printList(head);  // 5 → 10 → 20 → NULL
    
    deleteNode(&head, 10);
    printf("After Deletion: ");
    printList(head);  // 5 → 20 → NULL
    
    return 0;
}
```

---

### 2. **Doubly Linked List**
```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *prev;
    struct Node *next;
} Node;

Node* createNode(int value) {
    Node *newNode = (Node*)malloc(sizeof(Node));
    if (!newNode) {
        printf("Memory error\n");
        exit(1);
    }
    newNode->data = value;
    newNode->prev = NULL;
    newNode->next = NULL;
    return newNode;
}

void insertAtEnd(Node **head, int value) {
    Node *newNode = createNode(value);
    if (*head == NULL) {
        *head = newNode;
        return;
    }
    Node *temp = *head;
    while (temp->next != NULL) {
        temp = temp->next;
    }
    temp->next = newNode;
    newNode->prev = temp;
}

void deleteNode(Node **head, int value) {
    if (*head == NULL) {
        printf("List is empty\n");
        return;
    }
    Node *temp = *head;
    while (temp != NULL && temp->data != value) {
        temp = temp->next;
    }
    if (temp == NULL) {
        printf("Value not found\n");
        return;
    }
    if (temp->prev != NULL) {
        temp->prev->next = temp->next;
    } else {
        *head = temp->next;
    }
    if (temp->next != NULL) {
        temp->next->prev = temp->prev;
    }
    free(temp);
}

void printList(Node *head) {
    Node *temp = head;
    while (temp != NULL) {
        printf("%d ⇄ ", temp->data);
        temp = temp->next;
    }
    printf("NULL\n");
}

int main() {
    Node *head = NULL;
    
    insertAtEnd(&head, 10);
    insertAtEnd(&head, 20);
    insertAtEnd(&head, 30);
    
    printf("Original List: ");
    printList(head);  // 10 ⇄ 20 ⇄ 30 ⇄ NULL
    
    deleteNode(&head, 20);
    printf("After Deletion: ");
    printList(head);  // 10 ⇄ 30 ⇄ NULL
    
    return 0;
}
```

---

### 3. **Circular Linked List**
```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *next;
} Node;

Node* createNode(int value) {
    Node *newNode = (Node*)malloc(sizeof(Node));
    if (!newNode) {
        printf("Memory error\n");
        exit(1);
    }
    newNode->data = value;
    newNode->next = NULL;
    return newNode;
}

void insertAtEnd(Node **head, int value) {
    Node *newNode = createNode(value);
    if (*head == NULL) {
        *head = newNode;
        newNode->next = *head;
        return;
    }
    Node *temp = *head;
    while (temp->next != *head) {
        temp = temp->next;
    }
    temp->next = newNode;
    newNode->next = *head;
}

void printList(Node *head) {
    if (head == NULL) {
        printf("List is empty\n");
        return;
    }
    Node *temp = head;
    do {
        printf("%d → ", temp->data);
        temp = temp->next;
    } while (temp != head);
    printf("HEAD\n");
}

int main() {
    Node *head = NULL;
    
    insertAtEnd(&head, 10);
    insertAtEnd(&head, 20);
    insertAtEnd(&head, 30);
    
    printf("Circular List: ");
    printList(head);  // 10 → 20 → 30 → HEAD
    
    return 0;
}
```

---

## Applications of Linked Lists
1. **Dynamic Memory Allocation**: Manage memory blocks efficiently.
2. **Undo/Redo Functionality**: Used in editors (e.g., linked list of states).
3. **Hash Tables**: Handle collisions using chaining.
4. **Graphs**: Represent adjacency lists.
5. **Music/Video Playlists**: Navigate tracks sequentially.

---

## Comparison: Linked Lists vs. Arrays
| **Feature**          | **Linked List**                  | **Array**                     |
|----------------------|----------------------------------|-------------------------------|
| **Memory**           | Dynamic, no wasted space         | Fixed size, may waste space   |
| **Access Time**      | O(n) for random access           | O(1) for random access        |
| **Insert/Delete**    | O(1) at head, O(n) elsewhere     | O(n) for shifts               |
| **Memory Overhead**  | Extra space for pointers         | No overhead                   |

---

## Advanced Topics
1. **Memory Pool Allocation**: Pre-allocate nodes for efficiency.
2. **Skip Lists**: Multi-level linked lists for faster search (O(log n)).
3. **Self-Adjusting Lists**: Reorganize nodes based on access patterns (e.g., move-to-front).

---

## Conclusion
Linked lists provide **flexible memory management** and **efficient insertions/deletions**, making them ideal for applications requiring dynamic data handling. Choose between singly, doubly, or circular linked lists based on your need for bidirectional traversal or circular access patterns.
