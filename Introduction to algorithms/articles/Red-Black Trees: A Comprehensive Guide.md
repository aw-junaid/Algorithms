# Red-Black Trees: A Comprehensive Guide

Red-Black trees are a type of self-balancing binary search tree that maintain balance through specific properties and operations. They offer guaranteed O(log n) time complexity for search, insert, and delete operations.

## 1. Properties of Red-Black Trees

A Red-Black tree must satisfy the following properties:

1. **Node Color**: Each node is either red or black.
2. **Root Property**: The root is always black.
3. **Leaf Property**: All leaves (NIL nodes) are black.
4. **Red Property**: If a node is red, both its children must be black (no two red nodes can be adjacent).
5. **Path Property**: Every path from a node to its descendant NIL nodes must contain the same number of black nodes (black-height).

These properties ensure the tree remains approximately balanced, with the longest path being no more than twice as long as the shortest path.

## 2. Rotations

Rotations are fundamental operations used to maintain the Red-Black tree properties during insertions and deletions.

### Left Rotation

```c
void leftRotate(Node **root, Node *x) {
    Node *y = x->right;
    x->right = y->left;
    
    if (y->left != NULL)
        y->left->parent = x;
    
    y->parent = x->parent;
    
    if (x->parent == NULL)
        *root = y;
    else if (x == x->parent->left)
        x->parent->left = y;
    else
        x->parent->right = y;
    
    y->left = x;
    x->parent = y;
}
```

### Right Rotation

```c
void rightRotate(Node **root, Node *y) {
    Node *x = y->left;
    y->left = x->right;
    
    if (x->right != NULL)
        x->right->parent = y;
    
    x->parent = y->parent;
    
    if (y->parent == NULL)
        *root = x;
    else if (y == y->parent->left)
        y->parent->left = x;
    else
        y->parent->right = x;
    
    x->right = y;
    y->parent = x;
}
```

## 3. Insertion

Insertion in a Red-Black tree follows these steps:
1. Perform standard BST insertion.
2. Color the new node red.
3. Fix violations of Red-Black properties.

### Insertion Fixup Cases

There are several cases to consider when fixing violations after insertion:

1. **Case 1**: Uncle is red - Recolor parent, uncle, and grandparent.
2. **Case 2**: Uncle is black (triangle) - Rotate parent opposite of new node.
3. **Case 3**: Uncle is black (line) - Rotate grandparent and recolor.

### Implementation

```c
#include <stdio.h>
#include <stdlib.h>

typedef enum { RED, BLACK } Color;

typedef struct Node {
    int data;
    Color color;
    struct Node *left, *right, *parent;
} Node;

Node* createNode(int data) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->data = data;
    newNode->color = RED;
    newNode->left = newNode->right = newNode->parent = NULL;
    return newNode;
}

void insertFixup(Node **root, Node *z) {
    while (z != *root && z->parent->color == RED) {
        if (z->parent == z->parent->parent->left) {
            Node *y = z->parent->parent->right;
            if (y != NULL && y->color == RED) {
                // Case 1: Uncle is red
                z->parent->color = BLACK;
                y->color = BLACK;
                z->parent->parent->color = RED;
                z = z->parent->parent;
            } else {
                if (z == z->parent->right) {
                    // Case 2: Triangle case
                    z = z->parent;
                    leftRotate(root, z);
                }
                // Case 3: Line case
                z->parent->color = BLACK;
                z->parent->parent->color = RED;
                rightRotate(root, z->parent->parent);
            }
        } else {
            // Mirror cases
            Node *y = z->parent->parent->left;
            if (y != NULL && y->color == RED) {
                // Case 1
                z->parent->color = BLACK;
                y->color = BLACK;
                z->parent->parent->color = RED;
                z = z->parent->parent;
            } else {
                if (z == z->parent->left) {
                    // Case 2
                    z = z->parent;
                    rightRotate(root, z);
                }
                // Case 3
                z->parent->color = BLACK;
                z->parent->parent->color = RED;
                leftRotate(root, z->parent->parent);
            }
        }
    }
    (*root)->color = BLACK;
}

void insert(Node **root, int data) {
    Node *z = createNode(data);
    Node *y = NULL;
    Node *x = *root;
    
    while (x != NULL) {
        y = x;
        if (z->data < x->data)
            x = x->left;
        else
            x = x->right;
    }
    
    z->parent = y;
    if (y == NULL)
        *root = z;
    else if (z->data < y->data)
        y->left = z;
    else
        y->right = z;
    
    insertFixup(root, z);
}
```

## 4. Deletion

Deletion in a Red-Black tree is more complex than insertion. The steps are:

1. Perform standard BST deletion.
2. If the deleted node was black, fix violations.

### Deletion Fixup Cases

There are several cases to consider when fixing violations after deletion:

1. **Case 1**: Sibling is red - Recolor sibling and parent, then rotate parent.
2. **Case 2**: Sibling is black with two black children - Recolor sibling.
3. **Case 3**: Sibling is black with red child in opposite direction - Rotate sibling and recolor.
4. **Case 4**: Sibling is black with red child in same direction - Rotate parent and recolor.

### Implementation

```c
void transplant(Node **root, Node *u, Node *v) {
    if (u->parent == NULL)
        *root = v;
    else if (u == u->parent->left)
        u->parent->left = v;
    else
        u->parent->right = v;
    if (v != NULL)
        v->parent = u->parent;
}

Node* minimum(Node *node) {
    while (node->left != NULL)
        node = node->left;
    return node;
}

void deleteFixup(Node **root, Node *x) {
    while (x != *root && (x == NULL || x->color == BLACK)) {
        if (x == x->parent->left) {
            Node *w = x->parent->right;
            if (w->color == RED) {
                // Case 1: Sibling is red
                w->color = BLACK;
                x->parent->color = RED;
                leftRotate(root, x->parent);
                w = x->parent->right;
            }
            if ((w->left == NULL || w->left->color == BLACK) && 
                (w->right == NULL || w->right->color == BLACK)) {
                // Case 2: Both children of sibling are black
                w->color = RED;
                x = x->parent;
            } else {
                if (w->right == NULL || w->right->color == BLACK) {
                    // Case 3: Right child is black, left is red
                    if (w->left != NULL)
                        w->left->color = BLACK;
                    w->color = RED;
                    rightRotate(root, w);
                    w = x->parent->right;
                }
                // Case 4: Right child is red
                w->color = x->parent->color;
                x->parent->color = BLACK;
                if (w->right != NULL)
                    w->right->color = BLACK;
                leftRotate(root, x->parent);
                x = *root;
            }
        } else {
            // Mirror cases
            Node *w = x->parent->left;
            if (w->color == RED) {
                w->color = BLACK;
                x->parent->color = RED;
                rightRotate(root, x->parent);
                w = x->parent->left;
            }
            if ((w->right == NULL || w->right->color == BLACK) && 
                (w->left == NULL || w->left->color == BLACK)) {
                w->color = RED;
                x = x->parent;
            } else {
                if (w->left == NULL || w->left->color == BLACK) {
                    if (w->right != NULL)
                        w->right->color = BLACK;
                    w->color = RED;
                    leftRotate(root, w);
                    w = x->parent->left;
                }
                w->color = x->parent->color;
                x->parent->color = BLACK;
                if (w->left != NULL)
                    w->left->color = BLACK;
                rightRotate(root, x->parent);
                x = *root;
            }
        }
    }
    if (x != NULL)
        x->color = BLACK;
}

void delete(Node **root, int data) {
    Node *z = *root;
    while (z != NULL) {
        if (data < z->data)
            z = z->left;
        else if (data > z->data)
            z = z->right;
        else
            break;
    }
    if (z == NULL) return; // Node not found
    
    Node *y = z;
    Color yOriginalColor = y->color;
    Node *x;
    
    if (z->left == NULL) {
        x = z->right;
        transplant(root, z, z->right);
    } else if (z->right == NULL) {
        x = z->left;
        transplant(root, z, z->left);
    } else {
        y = minimum(z->right);
        yOriginalColor = y->color;
        x = y->right;
        if (y->parent == z) {
            if (x != NULL)
                x->parent = y;
        } else {
            transplant(root, y, y->right);
            y->right = z->right;
            y->right->parent = y;
        }
        transplant(root, z, y);
        y->left = z->left;
        y->left->parent = y;
        y->color = z->color;
    }
    
    free(z);
    
    if (yOriginalColor == BLACK)
        deleteFixup(root, x);
}
```

## Complete Implementation with Helper Functions

Here's a complete implementation with helper functions for printing the tree:

```c
#include <stdio.h>
#include <stdlib.h>

typedef enum { RED, BLACK } Color;

typedef struct Node {
    int data;
    Color color;
    struct Node *left, *right, *parent;
} Node;

// ... [Previous rotation, insert, and delete functions] ...

void printTreeHelper(Node *root, int space) {
    if (root == NULL) return;
    
    space += 10;
    printTreeHelper(root->right, space);
    printf("\n");
    for (int i = 10; i < space; i++)
        printf(" ");
    printf("%d(%s)\n", root->data, root->color == RED ? "R" : "B");
    printTreeHelper(root->left, space);
}

void printTree(Node *root) {
    printTreeHelper(root, 0);
}

int main() {
    Node *root = NULL;
    
    insert(&root, 7);
    insert(&root, 3);
    insert(&root, 18);
    insert(&root, 10);
    insert(&root, 22);
    insert(&root, 8);
    insert(&root, 11);
    insert(&root, 26);
    
    printf("Red-Black Tree after insertion:\n");
    printTree(root);
    
    delete(&root, 18);
    delete(&root, 11);
    delete(&root, 3);
    
    printf("\nRed-Black Tree after deletion:\n");
    printTree(root);
    
    return 0;
}
```

This implementation provides a complete Red-Black tree with insertion, deletion, and visualization capabilities. The tree maintains its balance properties through the carefully designed rotation and fixup operations.
