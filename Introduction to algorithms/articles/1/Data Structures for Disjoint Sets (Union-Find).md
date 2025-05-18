# Data Structures for Disjoint Sets (Union-Find)

Disjoint-set data structures, also known as union-find data structures, are used to maintain a collection of non-overlapping sets. They support three main operations:
1. MAKE-SET(x) - creates a new set containing element x
2. UNION(x, y) - merges the sets containing x and y
3. FIND-SET(x) - returns the representative of the set containing x

## Disjoint-Set Operations

### MAKE-SET(x)
Creates a new set whose only member is x. Since the sets are disjoint, x must not already be in any other set.

### UNION(x, y)
Unites the sets that contain x and y into a new set that is the union of these two sets. After this operation, the original sets containing x and y are destroyed.

### FIND-SET(x)
Returns a pointer to the representative of the (unique) set containing x. The representative is a member of the set that serves as its identifier.

## Linked-list Representation of Disjoint Sets

In this representation:
- Each set is represented as a linked list
- The first object in each list is the set representative
- Each object contains:
  - A set member
  - A pointer to the next object
  - A pointer back to the representative

**Operations:**
- MAKE-SET(x): O(1) - create a new list with x
- FIND-SET(x): O(1) - return the representative pointer
- UNION(x, y): O(n) - must append one list to another and update all representative pointers

The main disadvantage is that UNION operations can be expensive (O(n) time).

## Disjoint-Set Forests

A more efficient representation uses rooted trees:
- Each set is represented as a tree
- Each node points to its parent (root points to itself)
- The root is the set representative

To improve efficiency, two heuristics are used:
1. **Union by rank**: Attach the smaller tree to the root of the larger tree
2. **Path compression**: During FIND-SET, make each node point directly to the root

### Analysis of Union by Rank with Path Compression

With these optimizations, the amortized time per operation is nearly constant. Specifically, a sequence of m operations (MAKE-SET, UNION, FIND-SET) on n elements takes O(m α(n)) time, where α(n) is the extremely slow-growing inverse Ackermann function (for all practical purposes, α(n) ≤ 4).

## C Implementation

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    int rank;
    struct Node *parent;
} Node;

Node* makeSet(int data) {
    Node* node = (Node*)malloc(sizeof(Node));
    node->data = data;
    node->parent = node;
    node->rank = 0;
    return node;
}

Node* findSet(Node* node) {
    if (node != node->parent) {
        node->parent = findSet(node->parent); // Path compression
    }
    return node->parent;
}

void unionSets(Node* x, Node* y) {
    Node* xRoot = findSet(x);
    Node* yRoot = findSet(y);
    
    if (xRoot == yRoot) return; // Already in same set
    
    // Union by rank
    if (xRoot->rank > yRoot->rank) {
        yRoot->parent = xRoot;
    } else if (xRoot->rank < yRoot->rank) {
        xRoot->parent = yRoot;
    } else {
        yRoot->parent = xRoot;
        xRoot->rank++;
    }
}

int main() {
    // Example usage
    Node* nodes[10];
    
    // Create 10 singleton sets
    for (int i = 0; i < 10; i++) {
        nodes[i] = makeSet(i);
    }
    
    // Perform some unions
    unionSets(nodes[0], nodes[1]);
    unionSets(nodes[2], nodes[3]);
    unionSets(nodes[1], nodes[2]);
    unionSets(nodes[5], nodes[6]);
    unionSets(nodes[7], nodes[8]);
    unionSets(nodes[3], nodes[5]);
    unionSets(nodes[0], nodes[7]);
    
    // Find representatives
    for (int i = 0; i < 10; i++) {
        printf("Element %d is in set with representative %d\n", 
               i, findSet(nodes[i])->data);
    }
    
    // Clean up
    for (int i = 0; i < 10; i++) {
        free(nodes[i]);
    }
    
    return 0;
}
```

### Explanation of the Implementation:

1. **Node Structure**:
   - `data`: The value stored in the node
   - `rank`: An upper bound on the height of the node
   - `parent`: Pointer to the parent node (self for roots)

2. **makeSet**:
   - Creates a new set with a single element
   - Initializes rank to 0 and parent to itself

3. **findSet**:
   - Recursively finds the root representative
   - Implements path compression by making nodes point directly to the root

4. **unionSets**:
   - Finds the roots of both sets
   - Implements union by rank to keep the tree flat
   - If ranks are equal, arbitrarily chooses one as parent and increments its rank

The example in main() demonstrates creating sets, performing unions, and finding representatives. The output will show that elements 0-8 are all in the same set (with the same representative), while element 9 remains in its own set.
