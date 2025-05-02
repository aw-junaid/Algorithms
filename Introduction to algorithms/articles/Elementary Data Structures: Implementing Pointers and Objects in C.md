# Elementary Data Structures: Implementing Pointers and Objects in C

## Introduction to Pointers and Objects in C
In C, **pointers** and **structures (structs)** are fundamental for creating dynamic data structures. While C is not object-oriented, structs can mimic "objects" by encapsulating data, and pointers enable dynamic memory management and linking between elements.

### Key Concepts:
1. **Pointers**: Variables that store memory addresses.
2. **Structs**: User-defined types that group related data (analogous to objects).
3. **Dynamic Memory Allocation**: Using `malloc` and `free` to manage memory at runtime.

---

## 1. Pointers in C
A pointer holds the address of another variable. It is declared with a `*` and dereferenced with the same operator.

### Syntax:
```c
int x = 10;
int *ptr = &x;  // ptr stores the address of x
printf("%d", *ptr);  // Output: 10
```

### Uses:
- Dynamic memory allocation (`malloc`, `free`).
- Passing large structs efficiently (avoid copying).
- Creating linked data structures (e.g., linked lists, trees).

---

## 2. Structs as Objects
A `struct` groups variables of different types. It can contain pointers to other structs, enabling complex data structures.

### Example: Defining a Struct
```c
typedef struct Node {
    int data;
    struct Node *next;  // Pointer to another Node
} Node;
```

---

## 3. Combining Pointers and Structs
To create dynamic data structures:
1. Allocate memory for a struct using `malloc`.
2. Use pointers to link structs together.
3. Free memory with `free` to avoid leaks.

---

## 4. Dynamic Memory Allocation
- **`malloc(size)`**: Allocates `size` bytes of memory. Returns a `void*` pointer.
- **`free(ptr)`**: Releases memory pointed to by `ptr`.

### Example:
```c
Node *node = (Node*)malloc(sizeof(Node));
if (node == NULL) {
    // Handle allocation failure
}
free(node);  // Release memory
```

---

## Implementation: Linked List Using Pointers and Structs

### Code
```c
#include <stdio.h>
#include <stdlib.h>

// Define a Node struct (our "object")
typedef struct Node {
    int data;
    struct Node *next;
} Node;

// Create a new node
Node* createNode(int data) {
    Node *newNode = (Node*)malloc(sizeof(Node));
    if (newNode == NULL) {
        fprintf(stderr, "Memory allocation failed\n");
        exit(EXIT_FAILURE);
    }
    newNode->data = data;
    newNode->next = NULL;
    return newNode;
}

// Insert a node at the beginning of the list
void insertAtBeginning(Node **head, int data) {
    Node *newNode = createNode(data);
    newNode->next = *head;  // New node points to the old head
    *head = newNode;        // Update head to point to the new node
}

// Print the entire list
void printList(Node *head) {
    Node *current = head;
    while (current != NULL) {
        printf("%d -> ", current->data);
        current = current->next;
    }
    printf("NULL\n");
}

// Free the entire list to avoid memory leaks
void deleteList(Node **head) {
    Node *current = *head;
    Node *next;
    while (current != NULL) {
        next = current->next;  // Save the next node
        free(current);         // Free the current node
        current = next;        // Move to the next node
    }
    *head = NULL;  // Set head to NULL to avoid dangling pointers
}

int main() {
    Node *head = NULL;  // Initialize an empty list

    // Insert nodes
    insertAtBeginning(&head, 3);
    insertAtBeginning(&head, 2);
    insertAtBeginning(&head, 1);

    printf("Linked List: ");
    printList(head);  // Output: 1 -> 2 -> 3 -> NULL

    // Free all memory
    deleteList(&head);

    return 0;
}
```

---

## Code Explanation

### 1. Struct Definition
```c
typedef struct Node {
    int data;
    struct Node *next;
} Node;
```
- Defines a `Node` struct with `data` (integer) and `next` (pointer to another `Node`).

### 2. `createNode` Function
- Dynamically allocates memory for a new node.
- Initializes `data` and sets `next` to `NULL`.
- Checks for allocation failure.

### 3. `insertAtBeginning` Function
- Takes a **double pointer** (`Node **head`) to modify the original head pointer.
- Links the new node to the existing list and updates the head.

### 4. `printList` Function
- Traverses the list using a pointer (`current`).
- Prints each node’s data until reaching `NULL`.

### 5. `deleteList` Function
- Iterates through the list, freeing each node.
- Uses a temporary pointer (`next`) to avoid dereferencing freed memory.
- Sets `head` to `NULL` after deletion.

---

## Memory Management Best Practices
1. **Check for `NULL`**: Always verify `malloc`’s return value.
2. **Avoid Dangling Pointers**: Set pointers to `NULL` after freeing.
3. **Free All Allocations**: Ensure every `malloc` has a corresponding `free`.

---

## Advanced Concepts
1. **Double Pointers**: Used to modify the original pointer (e.g., updating the head of a list).
2. **Function Pointers**: Simulate object methods by passing struct pointers to functions.
3. **Self-Referential Structs**: Structs containing pointers to the same type (e.g., trees, graphs).

---

## Conclusion
In C, **pointers** and **structs** form the foundation for implementing dynamic data structures. By combining these concepts, you can create linked lists, trees, and other complex data layouts. Proper memory management is critical to avoid leaks and ensure efficient resource usage. This approach provides fine-grained control over memory, making C ideal for performance-critical applications.
