# Elementary Data Structures: Representing Rooted Trees

## Introduction to Rooted Trees
A **rooted tree** is a hierarchical data structure consisting of nodes connected by edges, where one node is designated as the **root**. Each node has:
- **Parent**: Except the root, which has no parent.
- **Children**: Zero or more child nodes.
- **Siblings**: Nodes sharing the same parent.
- **Leaves**: Nodes with no children.

### Key Terminology:
- **Root**: Topmost node with no parent.
- **Depth**: Number of edges from the root to the node.
- **Height**: Longest path from the node to a leaf.

---

## Tree Representations
### 1. **Left-Child Right-Sibling Representation**
- Each node stores:
  - `data`: Value of the node.
  - `firstChild`: Pointer to the first child.
  - `nextSibling`: Pointer to the next sibling.
- Efficient for dynamic trees with varying numbers of children.

### 2. **Array Representation**
- Used for complete binary trees.
- For node at index `i`:
  - Parent: `(i-1)/2`
  - Left child: `2i + 1`
  - Right child: `2i + 2`

### 3. **Parent Pointer Representation**
- Each node stores a pointer to its parent.
- Useful for upward traversal (e.g., finding the path to the root).

---

## Implementation in C (Left-Child Right-Sibling)

### 1. Node Structure
```c
#include <stdio.h>
#include <stdlib.h>

typedef struct TreeNode {
    int data;
    struct TreeNode *firstChild;
    struct TreeNode *nextSibling;
} TreeNode;
```

### 2. Create a Node
```c
TreeNode* createNode(int data) {
    TreeNode *newNode = (TreeNode*)malloc(sizeof(TreeNode));
    if (newNode == NULL) {
        fprintf(stderr, "Memory allocation failed\n");
        exit(EXIT_FAILURE);
    }
    newNode->data = data;
    newNode->firstChild = NULL;
    newNode->nextSibling = NULL;
    return newNode;
}
```

### 3. Append a Child to a Parent
```c
void appendChild(TreeNode *parent, TreeNode *child) {
    if (parent == NULL || child == NULL) return;
    if (parent->firstChild == NULL) {
        parent->firstChild = child;
    } else {
        TreeNode *lastChild = parent->firstChild;
        while (lastChild->nextSibling != NULL) {
            lastChild = lastChild->nextSibling;
        }
        lastChild->nextSibling = child;
    }
}
```

### 4. Pre-Order Traversal
```c
void preOrder(TreeNode *root) {
    if (root == NULL) return;
    printf("%d ", root->data);
    TreeNode *child = root->firstChild;
    while (child != NULL) {
        preOrder(child);
        child = child->nextSibling;
    }
}
```

### 5. Free the Tree
```c
void freeTree(TreeNode *root) {
    if (root == NULL) return;
    freeTree(root->firstChild);
    freeTree(root->nextSibling);
    free(root);
}
```

### 6. Example Usage
```c
int main() {
    // Create nodes
    TreeNode *root = createNode(1);
    TreeNode *node2 = createNode(2);
    TreeNode *node3 = createNode(3);
    TreeNode *node4 = createNode(4);
    TreeNode *node5 = createNode(5);

    // Build the tree
    appendChild(root, node2);
    appendChild(root, node3);
    appendChild(root, node4);
    appendChild(node2, node5);

    // Traverse and print
    printf("Pre-order traversal: ");
    preOrder(root);  // Output: 1 2 5 3 4

    // Free memory
    freeTree(root);

    return 0;
}
```

---

## Detailed Explanation

### 1. **Node Structure**
- `data`: Stores the node’s value.
- `firstChild`: Points to the first child node.
- `nextSibling`: Points to the next sibling node.

### 2. **Appending a Child**
- If the parent has no children, set `firstChild` to the new child.
- If the parent has children, traverse to the last child and link the new child as its sibling.

### 3. **Pre-Order Traversal**
1. Visit the root node.
2. Recursively traverse the subtree of the first child.
3. Move to the next sibling and repeat.

### 4. **Freeing the Tree**
- Use post-order traversal to delete children before the parent.
- Recursively free `firstChild` and `nextSibling`.

---

## Time Complexity
| **Operation**       | **Time Complexity** | **Description**                     |
|----------------------|---------------------|-------------------------------------|
| **Append Child**     | O(n)                | Traverses to the end of siblings    |
| **Pre-Order Traversal** | O(n)            | Visits every node exactly once      |
| **Free Tree**        | O(n)                | Deletes all nodes recursively       |

---

## Applications of Rooted Trees
1. **File Systems**: Directory structures.
2. **Organization Charts**: Hierarchical employee relationships.
3. **XML/HTML Parsing**: Nested tag structures.
4. **Game AI**: Decision trees for behavior.

---

## Comparison of Representations
| **Representation**           | **Pros**                          | **Cons**                          |
|-------------------------------|-----------------------------------|-----------------------------------|
| **Left-Child Right-Sibling**  | Dynamic, memory-efficient         | Sibling traversal can be slow     |
| **Array**                     | Fast access by index              | Wastes space for incomplete trees |
| **Parent Pointer**            | Efficient upward traversal        | No direct child access            |

---

## Conclusion
Rooted trees are versatile structures for modeling hierarchical relationships. The **left-child right-sibling** representation in C balances flexibility and efficiency, making it ideal for dynamic trees. Proper memory management ensures no leaks, and traversal algorithms like pre-order enable systematic processing of tree data.
