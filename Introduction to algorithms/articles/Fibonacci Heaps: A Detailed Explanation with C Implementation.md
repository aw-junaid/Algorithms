# Fibonacci Heaps: A Detailed Explanation with C Implementation

## 1. Structure of Fibonacci Heaps

A Fibonacci heap is a collection of min-heap-ordered trees (or max-heap-ordered for max-heaps). It has the following properties:

- **Min-heap property**: Each tree follows the min-heap property where the key of a child is greater than or equal to the key of its parent.
- **Root list**: The roots of all trees are connected using a circular, doubly linked list.
- **Min pointer**: A pointer to the tree root with the minimum key.
- **Marked nodes**: Nodes may be marked to help with efficiency during decrease-key operations.
- **Degree count**: Each node maintains a count of its children (degree).

### Node Structure:
Each node contains:
- Key/value
- Pointer to parent
- Pointer to one child (any child)
- Pointers to left and right siblings (for circular doubly linked list)
- Degree (number of children)
- Mark (boolean indicating whether node has lost a child since it became a child of another node)

## 2. Mergeable-Heap Operations

### a) MAKE-HEAP
Creates and returns a new Fibonacci heap containing no elements.

### b) INSERT
Adds a new element to the Fibonacci heap.

Steps:
1. Create a new node with the given key
2. Add the node to the root list
3. Update min pointer if necessary

### c) FIND-MIN
Returns the minimum node (just return the node pointed to by the min pointer).

### d) UNION
Merges two Fibonacci heaps into one.

Steps:
1. Concatenate the root lists of the two heaps
2. Update the min pointer to point to the smaller of the two minimum nodes

### e) EXTRACT-MIN
Removes and returns the node with the minimum key.

Steps:
1. Remove the min node and add its children to the root list
2. Consolidate the heap to combine trees of the same degree
3. Find the new min node by scanning the root list

## 3. Decreasing a Key and Deleting a Node

### a) DECREASE-KEY
Decreases the key of a given node.

Steps:
1. Reduce the node's key
2. If heap order is violated (new key is smaller than parent's key):
   - Cut the node and add it to the root list
   - If parent was marked, recursively cut the parent too (cascading cut)
   - Otherwise, mark the parent (unless it's a root)
3. Update min pointer if necessary

### b) DELETE
Deletes a node from the heap.

Steps:
1. Decrease the node's key to -∞ (or ∞ for max-heap)
2. Perform EXTRACT-MIN to remove it

## 4. Bounding the Maximum Degree

The maximum degree D(n) of any node in an n-node Fibonacci heap is O(log n). This is crucial for maintaining efficiency.

### Why:
- The size of a tree rooted at a node of degree k is at least F_{k+2} (k+2nd Fibonacci number)
- This grows exponentially with k, so k must be O(log n)
- This ensures that operations that scan all children of a node (like consolidation) remain efficient

## C Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <limits.h>

typedef struct fib_node {
    int key;
    int degree;
    bool mark;
    struct fib_node *parent;
    struct fib_node *child;
    struct fib_node *left;
    struct fib_node *right;
} FibNode;

typedef struct {
    FibNode *min;
    int n;
} FibonacciHeap;

// Helper function to create a new node
FibNode* new_node(int key) {
    FibNode *node = (FibNode*)malloc(sizeof(FibNode));
    node->key = key;
    node->degree = 0;
    node->mark = false;
    node->parent = NULL;
    node->child = NULL;
    node->left = node;
    node->right = node;
    return node;
}

// Create a new empty heap
FibonacciHeap* make_heap() {
    FibonacciHeap *H = (FibonacciHeap*)malloc(sizeof(FibonacciHeap));
    H->min = NULL;
    H->n = 0;
    return H;
}

// Insert a node into the heap
void insert(FibonacciHeap *H, FibNode *x) {
    if (H->min == NULL) {
        // Create a root list containing just x
        H->min = x;
    } else {
        // Insert x into H's root list
        H->min->left->right = x;
        x->left = H->min->left;
        x->right = H->min;
        H->min->left = x;
        
        // Update min pointer if necessary
        if (x->key < H->min->key) {
            H->min = x;
        }
    }
    H->n++;
}

// Union two heaps
FibonacciHeap* heap_union(FibonacciHeap *H1, FibonacciHeap *H2) {
    FibonacciHeap *H = make_heap();
    H->min = H1->min;
    
    // Concatenate root lists
    if (H->min != NULL && H2->min != NULL) {
        FibNode *H1_right = H1->min->right;
        FibNode *H2_left = H2->min->left;
        
        H1->min->right = H2->min;
        H2->min->left = H1->min;
        H1_right->left = H2_left;
        H2_left->right = H1_right;
    }
    
    // Set min pointer
    if (H1->min == NULL || (H2->min != NULL && H2->min->key < H1->min->key)) {
        H->min = H2->min;
    }
    
    H->n = H1->n + H2->n;
    
    free(H1);
    free(H2);
    
    return H;
}

// Link two trees (make y a child of x)
void fib_heap_link(FibonacciHeap *H, FibNode *y, FibNode *x) {
    // Remove y from root list
    y->left->right = y->right;
    y->right->left = y->left;
    
    // Make y a child of x
    if (x->child == NULL) {
        x->child = y;
        y->left = y;
        y->right = y;
    } else {
        y->left = x->child->left;
        y->right = x->child;
        x->child->left->right = y;
        x->child->left = y;
    }
    
    y->parent = x;
    x->degree++;
    y->mark = false;
}

// Consolidate the heap after extract-min
void consolidate(FibonacciHeap *H) {
    int max_degree = 100; // Should be O(log n), but we use a fixed size for simplicity
    FibNode *A[max_degree];
    for (int i = 0; i < max_degree; i++) {
        A[i] = NULL;
    }
    
    FibNode *w = H->min;
    FibNode *last = w->left;
    FibNode *x, *y;
    int d;
    
    do {
        x = w;
        w = w->right;
        d = x->degree;
        
        while (A[d] != NULL) {
            y = A[d];
            if (x->key > y->key) {
                FibNode *temp = x;
                x = y;
                y = temp;
            }
            fib_heap_link(H, y, x);
            A[d] = NULL;
            d++;
        }
        A[d] = x;
    } while (w != last);
    
    H->min = NULL;
    
    for (int i = 0; i < max_degree; i++) {
        if (A[i] != NULL) {
            if (H->min == NULL) {
                H->min = A[i];
                H->min->left = H->min;
                H->min->right = H->min;
            } else {
                // Insert A[i] into root list
                H->min->left->right = A[i];
                A[i]->left = H->min->left;
                A[i]->right = H->min;
                H->min->left = A[i];
                
                // Update min if needed
                if (A[i]->key < H->min->key) {
                    H->min = A[i];
                }
            }
        }
    }
}

// Extract the minimum node
FibNode* extract_min(FibonacciHeap *H) {
    FibNode *z = H->min;
    if (z != NULL) {
        // Add all children of z to root list
        FibNode *child = z->child;
        if (child != NULL) {
            FibNode *first_child = child;
            do {
                FibNode *next_child = child->right;
                // Add child to root list
                H->min->left->right = child;
                child->left = H->min->left;
                child->right = H->min;
                H->min->left = child;
                
                child->parent = NULL;
                child = next_child;
            } while (child != first_child);
        }
        
        // Remove z from root list
        z->left->right = z->right;
        z->right->left = z->left;
        
        if (z == z->right) {
            H->min = NULL;
        } else {
            H->min = z->right;
            consolidate(H);
        }
        H->n--;
    }
    return z;
}

// Cut a node from its parent and add to root list
void cut(FibonacciHeap *H, FibNode *x, FibNode *y) {
    // Remove x from child list of y
    if (x->right == x) {
        y->child = NULL;
    } else {
        x->left->right = x->right;
        x->right->left = x->left;
        if (y->child == x) {
            y->child = x->right;
        }
    }
    
    y->degree--;
    
    // Add x to root list
    H->min->left->right = x;
    x->left = H->min->left;
    x->right = H->min;
    H->min->left = x;
    
    x->parent = NULL;
    x->mark = false;
}

// Cascading cut - recursively cut marked parents
void cascading_cut(FibonacciHeap *H, FibNode *y) {
    FibNode *z = y->parent;
    if (z != NULL) {
        if (y->mark == false) {
            y->mark = true;
        } else {
            cut(H, y, z);
            cascading_cut(H, z);
        }
    }
}

// Decrease key of a node
void decrease_key(FibonacciHeap *H, FibNode *x, int k) {
    if (k > x->key) {
        printf("New key is greater than current key\n");
        return;
    }
    
    x->key = k;
    FibNode *y = x->parent;
    
    if (y != NULL && x->key < y->key) {
        cut(H, x, y);
        cascading_cut(H, y);
    }
    
    if (x->key < H->min->key) {
        H->min = x;
    }
}

// Delete a node from the heap
void delete_node(FibonacciHeap *H, FibNode *x) {
    decrease_key(H, x, INT_MIN);
    FibNode *min = extract_min(H);
    free(min); // If you want to free the memory
}

// Helper function to print the heap
void print_tree(FibNode *node, int depth) {
    if (node == NULL) return;
    
    FibNode *current = node;
    do {
        for (int i = 0; i < depth; i++) printf("  ");
        printf("%d(%d)%s\n", current->key, current->degree, current->mark ? "*" : "");
        if (current->child != NULL) {
            print_tree(current->child, depth + 1);
        }
        current = current->right;
    } while (current != node);
}

void print_heap(FibonacciHeap *H) {
    if (H->min == NULL) {
        printf("Heap is empty\n");
        return;
    }
    printf("Fibonacci heap (min=%d):\n", H->min->key);
    print_tree(H->min, 0);
    printf("Total nodes: %d\n", H->n);
}

int main() {
    FibonacciHeap *H = make_heap();
    
    // Insert some nodes
    FibNode *nodes[10];
    for (int i = 0; i < 10; i++) {
        nodes[i] = new_node(i+1);
        insert(H, nodes[i]);
    }
    
    printf("Initial heap:\n");
    print_heap(H);
    
    // Extract min
    FibNode *min = extract_min(H);
    printf("\nExtracted min: %d\n", min->key);
    print_heap(H);
    
    // Decrease key
    printf("\nDecreasing key 7 to 3:\n");
    decrease_key(H, nodes[6], 3);
    print_heap(H);
    
    // Delete node
    printf("\nDeleting node with key 5:\n");
    delete_node(H, nodes[4]);
    print_heap(H);
    
    return 0;
}
```

### Key Points About the Implementation:

1. **Node Structure**: Each node maintains pointers to its parent, one child, left and right siblings, along with degree and mark fields.

2. **Circular Doubly Linked List**: The root list and each child list is implemented as a circular doubly linked list for efficient merging and traversal.

3. **Consolidation**: After extract-min, the heap is consolidated to combine trees of the same degree, which is crucial for maintaining efficiency.

4. **Cascading Cuts**: During decrease-key, marked parents are recursively cut to maintain the heap structure, which helps keep the amortized time complexity low.

5. **Memory Management**: The implementation includes proper memory allocation and deallocation, though a production implementation might need more robust error handling.

This implementation provides all the core operations of a Fibonacci heap with their amortized time complexities (O(1) for most operations, O(log n) for extract-min and delete).
