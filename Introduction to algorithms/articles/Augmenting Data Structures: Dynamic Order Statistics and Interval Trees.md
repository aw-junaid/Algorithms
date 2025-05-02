# Augmenting Data Structures: Dynamic Order Statistics and Interval Trees

## 1. Dynamic Order Statistics

Dynamic order statistics extend traditional data structures to support additional operations that involve the rank or position of elements in a sorted order. The key idea is to augment each node in a data structure (typically a binary search tree) with additional information that allows efficient computation of order-based operations.

### Key Operations:
- `OS-SELECT(x, i)`: Find the element with rank `i` in the subtree rooted at `x`
- `OS-RANK(T, x)`: Find the rank of element `x` in the tree `T`

### Augmentation:
Each node stores an additional field `size` which represents the number of nodes in the subtree rooted at that node (including itself).

### Implementation in C:

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int key;
    struct Node *left, *right, *parent;
    int size; // augmentation for order statistics
} Node;

Node* createNode(int key) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->key = key;
    newNode->left = newNode->right = newNode->parent = NULL;
    newNode->size = 1;
    return newNode;
}

void updateSize(Node* x) {
    if (x == NULL) return;
    x->size = 1;
    if (x->left) x->size += x->left->size;
    if (x->right) x->size += x->right->size;
}

Node* osSelect(Node* x, int i) {
    if (x == NULL) return NULL;
    
    int r = 1;
    if (x->left) r = x->left->size + 1;
    
    if (i == r) return x;
    else if (i < r) return osSelect(x->left, i);
    else return osSelect(x->right, i - r);
}

int osRank(Node* root, Node* x) {
    if (x == NULL) return 0;
    
    int r = 1;
    if (x->left) r += x->left->size;
    
    Node* y = x;
    while (y != root) {
        if (y == y->parent->right) {
            r += 1;
            if (y->parent->left) r += y->parent->left->size;
        }
        y = y->parent;
    }
    return r;
}

// Standard BST insert with size updates
Node* insert(Node* root, int key) {
    if (root == NULL) return createNode(key);
    
    if (key < root->key) {
        root->left = insert(root->left, key);
        root->left->parent = root;
    } else if (key > root->key) {
        root->right = insert(root->right, key);
        root->right->parent = root;
    }
    
    updateSize(root);
    return root;
}

// Example usage
int main() {
    Node* root = NULL;
    int keys[] = {5, 3, 7, 2, 4, 6, 8};
    int n = sizeof(keys)/sizeof(keys[0]);
    
    for (int i = 0; i < n; i++) {
        root = insert(root, keys[i]);
    }
    
    for (int i = 1; i <= n; i++) {
        Node* selected = osSelect(root, i);
        printf("Element with rank %d: %d\n", i, selected->key);
    }
    
    Node* node4 = root->left->right; // node with key 4
    printf("Rank of node with key 4: %d\n", osRank(root, node4));
    
    return 0;
}
```

## 2. How to Augment a Data Structure

Augmenting a data structure involves four steps:

1. **Choose an underlying data structure** (e.g., binary search tree, linked list)
2. **Determine additional information** to store in each node
3. **Verify the additional information can be maintained** during modification operations
4. **Develop new operations** that use the additional information

### Guidelines:
- The additional information should support the new operations you want to implement
- The information should be maintainable with reasonable efficiency during insertions, deletions, and other modifications
- The augmentation shouldn't significantly increase the space complexity

## 3. Interval Trees

Interval trees are an augmented data structure designed to efficiently store and search intervals. They support operations like finding all intervals that overlap with a given interval or point.

### Augmentation:
Each node stores:
- An interval `[low, high]`
- The maximum endpoint (`max`) of any interval stored in the subtree rooted at that node

### Operations:
- `INTERVAL-INSERT(T, x)`: Insert interval `x` into tree `T`
- `INTERVAL-DELETE(T, x)`: Delete interval `x` from tree `T`
- `INTERVAL-SEARCH(T, i)`: Find an interval in tree `T` that overlaps with interval `i`

### Implementation in C:

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

typedef struct Interval {
    int low, high;
} Interval;

typedef struct ITNode {
    Interval *i;
    int max;
    struct ITNode *left, *right;
} ITNode;

ITNode* createITNode(Interval i) {
    ITNode* newNode = (ITNode*)malloc(sizeof(ITNode));
    newNode->i = (Interval*)malloc(sizeof(Interval));
    *(newNode->i) = i;
    newNode->max = i.high;
    newNode->left = newNode->right = NULL;
    return newNode;
}

void updateMax(ITNode* node) {
    if (node == NULL) return;
    
    int max = node->i->high;
    if (node->left && node->left->max > max) max = node->left->max;
    if (node->right && node->right->max > max) max = node->right->max;
    
    node->max = max;
}

ITNode* insert(ITNode* root, Interval i) {
    if (root == NULL) return createITNode(i);
    
    int l = root->i->low;
    if (i.low < l) {
        root->left = insert(root->left, i);
    } else {
        root->right = insert(root->right, i);
    }
    
    updateMax(root);
    return root;
}

bool doOverlap(Interval i1, Interval i2) {
    return (i1.low <= i2.high && i2.low <= i1.high);
}

Interval* intervalSearch(ITNode* root, Interval i) {
    if (root == NULL) return NULL;
    
    if (doOverlap(*(root->i), i)) return root->i;
    
    if (root->left != NULL && root->left->max >= i.low)
        return intervalSearch(root->left, i);
    
    return intervalSearch(root->right, i);
}

void inorder(ITNode* root) {
    if (root == NULL) return;
    
    inorder(root->left);
    printf("[%d, %d] max = %d\n", root->i->low, root->i->high, root->max);
    inorder(root->right);
}

// Example usage
int main() {
    Interval intervals[] = {{15, 20}, {10, 30}, {17, 19}, 
                           {5, 20}, {12, 15}, {30, 40}};
    int n = sizeof(intervals)/sizeof(intervals[0]);
    ITNode* root = NULL;
    
    for (int i = 0; i < n; i++) {
        root = insert(root, intervals[i]);
    }
    
    printf("Inorder traversal of interval tree:\n");
    inorder(root);
    
    Interval searchInterval = {6, 7};
    Interval* result = intervalSearch(root, searchInterval);
    if (result == NULL) {
        printf("\nNo overlapping interval with [%d, %d]\n", 
               searchInterval.low, searchInterval.high);
    } else {
        printf("\nOverlaps with [%d, %d]\n", result->low, result->high);
    }
    
    searchInterval = (Interval){21, 23};
    result = intervalSearch(root, searchInterval);
    if (result == NULL) {
        printf("No overlapping interval with [%d, %d]\n", 
               searchInterval.low, searchInterval.high);
    } else {
        printf("Overlaps with [%d, %d]\n", result->low, result->high);
    }
    
    return 0;
}
```

### Explanation of Key Components:

1. **Dynamic Order Statistics**:
   - The `size` field in each node allows efficient computation of rank and selection operations
   - `OS-SELECT` works by comparing the desired rank with the size of the left subtree
   - `OS-RANK` counts all elements to the left of the given node by traversing up the tree

2. **Interval Trees**:
   - The `max` field stores the maximum endpoint in the subtree, enabling efficient pruning of the search space
   - The search operation checks the left subtree only if there could potentially be an overlapping interval there
   - Insertions maintain the `max` fields through the `updateMax` function

Both augmentations demonstrate how adding carefully chosen information to nodes can enable new operations without significantly affecting the time complexity of existing operations.
