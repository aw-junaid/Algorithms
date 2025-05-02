# Binary Search Trees: A Comprehensive Guide

## 1. What is a Binary Search Tree?

A Binary Search Tree (BST) is a node-based binary tree data structure that maintains the following properties:
- Each node has at most two children (left and right)
- For any given node:
  - All values in the left subtree are less than the node's value
  - All values in the right subtree are greater than the node's value
- There are no duplicate nodes (typically)

BSTs provide efficient search, insertion, and deletion operations with average time complexity of O(log n) when the tree is balanced.

### Key Characteristics:
- **Ordering Property**: Maintains elements in sorted order
- **Dynamic Structure**: Grows and shrinks as needed
- **Efficient Operations**: Faster than linear structures for search/insert/delete when balanced

### BST Node Structure in C:
```c
typedef struct Node {
    int data;
    struct Node *left;
    struct Node *right;
} Node;
```

## 2. Querying a Binary Search Tree

### Search Operation
To find a value in a BST:
1. Start at the root
2. Compare target value with current node:
   - If equal, found
   - If less, go to left child
   - If greater, go to right child
3. Repeat until found or reach NULL pointer

### Implementation:
```c
Node* search(Node* root, int value) {
    if (root == NULL || root->data == value)
        return root;
    
    if (value < root->data)
        return search(root->left, value);
    else
        return search(root->right, value);
}
```

### Minimum and Maximum
- **Minimum**: Follow left children until NULL
- **Maximum**: Follow right children until NULL

```c
Node* minValueNode(Node* node) {
    Node* current = node;
    while (current && current->left != NULL)
        current = current->left;
    return current;
}

Node* maxValueNode(Node* node) {
    Node* current = node;
    while (current && current->right != NULL)
        current = current->right;
    return current;
}
```

### Successor and Predecessor
- **Successor**: Next higher value in tree
- **Predecessor**: Next lower value in tree

```c
Node* successor(Node* root, Node* node) {
    if (node->right != NULL)
        return minValueNode(node->right);
    
    Node* succ = NULL;
    while (root != NULL) {
        if (node->data < root->data) {
            succ = root;
            root = root->left;
        }
        else if (node->data > root->data)
            root = root->right;
        else
            break;
    }
    return succ;
}
```

## 3. Insertion and Deletion

### Insertion Operation
1. Start at root
2. Compare new value with current node
3. If less, go left; if greater, go right
4. When NULL is reached, insert new node

```c
Node* insert(Node* node, int value) {
    if (node == NULL) {
        Node* newNode = (Node*)malloc(sizeof(Node));
        newNode->data = value;
        newNode->left = newNode->right = NULL;
        return newNode;
    }
    
    if (value < node->data)
        node->left = insert(node->left, value);
    else if (value > node->data)
        node->right = insert(node->right, value);
    
    return node;
}
```

### Deletion Operation
Three cases:
1. **Node is a leaf**: Simply remove it
2. **Node has one child**: Replace with its child
3. **Node has two children**: Replace with its in-order successor/predecessor, then delete that node

```c
Node* deleteNode(Node* root, int value) {
    if (root == NULL) return root;
    
    if (value < root->data)
        root->left = deleteNode(root->left, value);
    else if (value > root->data)
        root->right = deleteNode(root->right, value);
    else {
        // Node with only one child or no child
        if (root->left == NULL) {
            Node* temp = root->right;
            free(root);
            return temp;
        }
        else if (root->right == NULL) {
            Node* temp = root->left;
            free(root);
            return temp;
        }
        
        // Node with two children
        Node* temp = minValueNode(root->right);
        root->data = temp->data;
        root->right = deleteNode(root->right, temp->data);
    }
    return root;
}
```

## 4. Randomly Built Binary Search Trees

A randomly built BST is constructed by inserting keys in random order. The average-case performance of operations on such trees is O(log n).

### Properties:
- **Expected height**: O(log n) for n nodes
- **Balanced**: More likely to be balanced than inserting in sorted order
- **Performance**: Random insertion order avoids worst-case O(n) scenarios

### Building a Random BST:
```c
#include <stdlib.h>
#include <time.h>

// Helper function to shuffle array
void shuffle(int arr[], int n) {
    srand(time(NULL));
    for (int i = n - 1; i > 0; i--) {
        int j = rand() % (i + 1);
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}

Node* buildRandomBST(int values[], int n) {
    shuffle(values, n);
    Node* root = NULL;
    for (int i = 0; i < n; i++) {
        root = insert(root, values[i]);
    }
    return root;
}
```

## Complete Implementation

Here's a complete C implementation with all operations:

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

typedef struct Node {
    int data;
    struct Node *left;
    struct Node *right;
} Node;

Node* createNode(int value) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->data = value;
    newNode->left = newNode->right = NULL;
    return newNode;
}

Node* insert(Node* node, int value) {
    if (node == NULL) return createNode(value);
    
    if (value < node->data)
        node->left = insert(node->left, value);
    else if (value > node->data)
        node->right = insert(node->right, value);
    
    return node;
}

Node* search(Node* root, int value) {
    if (root == NULL || root->data == value)
        return root;
    
    if (value < root->data)
        return search(root->left, value);
    else
        return search(root->right, value);
}

Node* minValueNode(Node* node) {
    Node* current = node;
    while (current && current->left != NULL)
        current = current->left;
    return current;
}

Node* deleteNode(Node* root, int value) {
    if (root == NULL) return root;
    
    if (value < root->data)
        root->left = deleteNode(root->left, value);
    else if (value > root->data)
        root->right = deleteNode(root->right, value);
    else {
        if (root->left == NULL) {
            Node* temp = root->right;
            free(root);
            return temp;
        }
        else if (root->right == NULL) {
            Node* temp = root->left;
            free(root);
            return temp;
        }
        
        Node* temp = minValueNode(root->right);
        root->data = temp->data;
        root->right = deleteNode(root->right, temp->data);
    }
    return root;
}

void inorderTraversal(Node* root) {
    if (root != NULL) {
        inorderTraversal(root->left);
        printf("%d ", root->data);
        inorderTraversal(root->right);
    }
}

void shuffle(int arr[], int n) {
    srand(time(NULL));
    for (int i = n - 1; i > 0; i--) {
        int j = rand() % (i + 1);
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}

Node* buildRandomBST(int values[], int n) {
    shuffle(values, n);
    Node* root = NULL;
    for (int i = 0; i < n; i++) {
        root = insert(root, values[i]);
    }
    return root;
}

int main() {
    int values[] = {50, 30, 20, 40, 70, 60, 80};
    int n = sizeof(values)/sizeof(values[0]);
    
    printf("Building BST with values: ");
    for (int i = 0; i < n; i++) printf("%d ", values[i]);
    printf("\n");
    
    Node* root = buildRandomBST(values, n);
    
    printf("Inorder traversal: ");
    inorderTraversal(root);
    printf("\n");
    
    printf("Searching for 40: ");
    if (search(root, 40)) printf("Found\n");
    else printf("Not found\n");
    
    printf("Deleting 20\n");
    root = deleteNode(root, 20);
    printf("Inorder traversal after deletion: ");
    inorderTraversal(root);
    printf("\n");
    
    printf("Inserting 25\n");
    root = insert(root, 25);
    printf("Inorder traversal after insertion: ");
    inorderTraversal(root);
    printf("\n");
    
    return 0;
}
```

## Time Complexity Analysis

| Operation  | Average Case | Worst Case |
|------------|--------------|------------|
| Search     | O(log n)     | O(n)       |
| Insert     | O(log n)     | O(n)       |
| Delete     | O(log n)     | O(n)       |
| Space      | O(n)         | O(n)       |

The worst case occurs when the tree becomes skewed (essentially a linked list), which can happen with sorted insertions. Randomly built BSTs help prevent this scenario. For guaranteed O(log n) performance, balanced BST variants like AVL trees or Red-Black trees are used.
