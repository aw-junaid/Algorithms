# B-Trees: A Comprehensive Guide

## Definition of B-Trees

A B-tree is a self-balancing tree data structure that maintains sorted data and allows for efficient insertion, deletion, and search operations. It is particularly well-suited for storage systems that read and write relatively large blocks of data, such as databases and file systems.

### Properties of B-Trees:

1. **Degree (Order)**: A B-tree is defined by a minimum degree `t` (where `t ≥ 2`)
2. **Node Structure**:
   - Every node has at most `2t - 1` keys
   - Every node (except root) has at least `t - 1` keys
   - The root may have as few as 1 key if it's not a leaf
3. **Height Balance**: All leaves are at the same level
4. **Key Distribution**:
   - Keys in a node are stored in sorted order
   - Between two keys `k₁` and `k₂` in an internal node, all keys in the subtree must be greater than `k₁` and less than `k₂`

## Basic Operations on B-Trees

### 1. Search Operation
- Similar to binary search tree but generalized for multiple keys per node
- Time complexity: O(logₜ n) where t is the minimum degree

### 2. Insertion Operation
- Always inserted at leaf level
- May require splitting nodes to maintain B-tree properties
- Time complexity: O(logₜ n)

### 3. Deletion Operation
- More complex than insertion
- May require borrowing from siblings or merging nodes
- Time complexity: O(logₜ n)

## Deleting a Key from a B-Tree

Deletion from a B-tree is more involved than insertion because we must ensure the tree remains balanced and all B-tree properties are maintained. Here's the detailed process:

### Cases for Deletion:

1. **Key is in a leaf node**:
   - If the leaf has more than `t-1` keys, simply remove the key
   - If the leaf has exactly `t-1` keys, we may need to:
     - Borrow a key from an immediate sibling (if sibling has more than `t-1` keys)
     - Merge with a sibling (if both have `t-1` keys)

2. **Key is in an internal node**:
   - Replace the key with its predecessor (largest key in left subtree) or successor (smallest key in right subtree)
   - Recursively delete the predecessor/successor (which will always be in a leaf)

3. **Key is not present in the node where expected**:
   - If the child that would contain the key has only `t-1` keys:
     - Borrow a key from an immediate sibling (if possible)
     - Merge with a sibling (if borrowing isn't possible)
   - Recursively proceed to the appropriate child

### Deletion Algorithm Steps:

1. Start at the root
2. If the key is in the current node:
   - If it's a leaf node, remove the key
   - If it's an internal node:
     a. Find predecessor or successor
     b. Replace the key with the predecessor/successor
     c. Recursively delete the predecessor/successor
3. If the key is not in the current node:
   - Find the child that would contain the key
   - Ensure this child has at least `t` keys (borrow or merge if necessary)
   - Recursively proceed to the appropriate child

## C Implementation of B-Tree with Deletion

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

#define MIN_DEGREE 3
#define MAX_KEYS (2*MIN_DEGREE-1)
#define MIN_KEYS (MIN_DEGREE-1)

typedef struct BTreeNode {
    int keys[MAX_KEYS];
    struct BTreeNode *children[MAX_KEYS + 1];
    int num_keys;
    bool is_leaf;
} BTreeNode;

// Create a new empty node
BTreeNode* create_node(bool is_leaf) {
    BTreeNode* node = (BTreeNode*)malloc(sizeof(BTreeNode));
    node->num_keys = 0;
    node->is_leaf = is_leaf;
    for (int i = 0; i < MAX_KEYS + 1; i++) {
        node->children[i] = NULL;
    }
    return node;
}

// Search for a key in the tree
bool search(BTreeNode* root, int key) {
    int i = 0;
    while (i < root->num_keys && key > root->keys[i]) {
        i++;
    }
    
    if (i < root->num_keys && key == root->keys[i]) {
        return true;
    }
    
    if (root->is_leaf) {
        return false;
    }
    
    return search(root->children[i], key);
}

// Insert a key into the tree (non-full node)
void insert_non_full(BTreeNode* node, int key) {
    int i = node->num_keys - 1;
    
    if (node->is_leaf) {
        while (i >= 0 && key < node->keys[i]) {
            node->keys[i + 1] = node->keys[i];
            i--;
        }
        node->keys[i + 1] = key;
        node->num_keys++;
    } else {
        while (i >= 0 && key < node->keys[i]) {
            i--;
        }
        i++;
        
        if (node->children[i]->num_keys == MAX_KEYS) {
            split_child(node, i);
            if (key > node->keys[i]) {
                i++;
            }
        }
        insert_non_full(node->children[i], key);
    }
}

// Split a full child of a node
void split_child(BTreeNode* parent, int child_index) {
    BTreeNode* child = parent->children[child_index];
    BTreeNode* new_child = create_node(child->is_leaf);
    new_child->num_keys = MIN_KEYS;
    
    // Copy the right half of keys to the new child
    for (int i = 0; i < MIN_KEYS; i++) {
        new_child->keys[i] = child->keys[i + MIN_DEGREE];
    }
    
    // Copy the right half of children if not leaf
    if (!child->is_leaf) {
        for (int i = 0; i < MIN_DEGREE; i++) {
            new_child->children[i] = child->children[i + MIN_DEGREE];
        }
    }
    
    child->num_keys = MIN_KEYS;
    
    // Make space for the new child in the parent
    for (int i = parent->num_keys; i > child_index; i--) {
        parent->children[i + 1] = parent->children[i];
    }
    parent->children[child_index + 1] = new_child;
    
    // Move the middle key of child to parent
    for (int i = parent->num_keys - 1; i >= child_index; i--) {
        parent->keys[i + 1] = parent->keys[i];
    }
    parent->keys[child_index] = child->keys[MIN_KEYS];
    parent->num_keys++;
}

// Insert a key into the tree
void insert(BTreeNode** root, int key) {
    BTreeNode* node = *root;
    
    if (node->num_keys == MAX_KEYS) {
        BTreeNode* new_root = create_node(false);
        new_root->children[0] = node;
        *root = new_root;
        split_child(new_root, 0);
        insert_non_full(new_root, key);
    } else {
        insert_non_full(node, key);
    }
}

// Find the predecessor of a key in a node
int get_predecessor(BTreeNode* node, int index) {
    BTreeNode* current = node->children[index];
    while (!current->is_leaf) {
        current = current->children[current->num_keys];
    }
    return current->keys[current->num_keys - 1];
}

// Find the successor of a key in a node
int get_successor(BTreeNode* node, int index) {
    BTreeNode* current = node->children[index + 1];
    while (!current->is_leaf) {
        current = current->children[0];
    }
    return current->keys[0];
}

// Merge a child with its sibling
void merge(BTreeNode* node, int index) {
    BTreeNode* child = node->children[index];
    BTreeNode* sibling = node->children[index + 1];
    
    // Bring the key from the parent down
    child->keys[MIN_KEYS] = node->keys[index];
    
    // Copy keys from sibling
    for (int i = 0; i < sibling->num_keys; i++) {
        child->keys[i + MIN_DEGREE] = sibling->keys[i];
    }
    
    // Copy children from sibling if not leaf
    if (!child->is_leaf) {
        for (int i = 0; i <= sibling->num_keys; i++) {
            child->children[i + MIN_DEGREE] = sibling->children[i];
        }
    }
    
    // Move keys in parent
    for (int i = index + 1; i < node->num_keys; i++) {
        node->keys[i - 1] = node->keys[i];
    }
    
    // Move children in parent
    for (int i = index + 2; i <= node->num_keys; i++) {
        node->children[i - 1] = node->children[i];
    }
    
    child->num_keys += sibling->num_keys + 1;
    node->num_keys--;
    
    free(sibling);
}

// Borrow a key from the left sibling
void borrow_from_left(BTreeNode* node, int index) {
    BTreeNode* child = node->children[index];
    BTreeNode* sibling = node->children[index - 1];
    
    // Move all keys in child right by 1
    for (int i = child->num_keys - 1; i >= 0; i--) {
        child->keys[i + 1] = child->keys[i];
    }
    
    // Move all children right by 1 if not leaf
    if (!child->is_leaf) {
        for (int i = child->num_keys; i >= 0; i--) {
            child->children[i + 1] = child->children[i];
        }
    }
    
    // Move key from parent to child
    child->keys[0] = node->keys[index - 1];
    
    // Move last key from sibling to parent
    node->keys[index - 1] = sibling->keys[sibling->num_keys - 1];
    
    // Move last child from sibling if not leaf
    if (!child->is_leaf) {
        child->children[0] = sibling->children[sibling->num_keys];
    }
    
    child->num_keys++;
    sibling->num_keys--;
}

// Borrow a key from the right sibling
void borrow_from_right(BTreeNode* node, int index) {
    BTreeNode* child = node->children[index];
    BTreeNode* sibling = node->children[index + 1];
    
    // Move key from parent to child
    child->keys[child->num_keys] = node->keys[index];
    
    // Move first key from sibling to parent
    node->keys[index] = sibling->keys[0];
    
    // Move first child from sibling if not leaf
    if (!child->is_leaf) {
        child->children[child->num_keys + 1] = sibling->children[0];
    }
    
    // Move all keys in sibling left by 1
    for (int i = 1; i < sibling->num_keys; i++) {
        sibling->keys[i - 1] = sibling->keys[i];
    }
    
    // Move all children left by 1 if not leaf
    if (!sibling->is_leaf) {
        for (int i = 1; i <= sibling->num_keys; i++) {
            sibling->children[i - 1] = sibling->children[i];
        }
    }
    
    child->num_keys++;
    sibling->num_keys--;
}

// Fill a child that has less than t-1 keys
void fill(BTreeNode* node, int index) {
    // If previous child has more than t-1 keys, borrow from it
    if (index != 0 && node->children[index - 1]->num_keys >= MIN_DEGREE) {
        borrow_from_left(node, index);
    }
    // If next child has more than t-1 keys, borrow from it
    else if (index != node->num_keys && node->children[index + 1]->num_keys >= MIN_DEGREE) {
        borrow_from_right(node, index);
    }
    // Otherwise, merge with a sibling
    else {
        if (index != node->num_keys) {
            merge(node, index);
        } else {
            merge(node, index - 1);
        }
    }
}

// Delete a key from a subtree
void delete_from_subtree(BTreeNode* node, int key) {
    int index = 0;
    
    // Find the key in the node or the child to descend to
    while (index < node->num_keys && key > node->keys[index]) {
        index++;
    }
    
    // Case 1: Key is present in this node
    if (index < node->num_keys && key == node->keys[index]) {
        if (node->is_leaf) {
            // Case 1a: Key is in a leaf node
            for (int i = index + 1; i < node->num_keys; i++) {
                node->keys[i - 1] = node->keys[i];
            }
            node->num_keys--;
        } else {
            // Case 1b: Key is in an internal node
            if (node->children[index]->num_keys >= MIN_DEGREE) {
                // Case 1b-i: Left child has at least t keys
                int predecessor = get_predecessor(node, index);
                node->keys[index] = predecessor;
                delete_from_subtree(node->children[index], predecessor);
            } else if (node->children[index + 1]->num_keys >= MIN_DEGREE) {
                // Case 1b-ii: Right child has at least t keys
                int successor = get_successor(node, index);
                node->keys[index] = successor;
                delete_from_subtree(node->children[index + 1], successor);
            } else {
                // Case 1b-iii: Both children have t-1 keys
                merge(node, index);
                delete_from_subtree(node->children[index], key);
            }
        }
    } else {
        // Case 2: Key is not present in this node
        if (node->is_leaf) {
            printf("Key %d not found in the tree.\n", key);
            return;
        }
        
        bool is_last_child = (index == node->num_keys);
        
        if (node->children[index]->num_keys < MIN_DEGREE) {
            fill(node, index);
        }
        
        // If the last child was merged, it must have merged with the previous child
        if (is_last_child && index > node->num_keys) {
            delete_from_subtree(node->children[index - 1], key);
        } else {
            delete_from_subtree(node->children[index], key);
        }
    }
}

// Delete a key from the tree
void delete_key(BTreeNode** root, int key) {
    if (!*root) {
        printf("Tree is empty.\n");
        return;
    }
    
    delete_from_subtree(*root, key);
    
    // If the root has no keys and has a child, make the child the new root
    if ((*root)->num_keys == 0) {
        BTreeNode* old_root = *root;
        if ((*root)->is_leaf) {
            *root = NULL;
        } else {
            *root = (*root)->children[0];
        }
        free(old_root);
    }
}

// Print the tree (inorder traversal)
void print_tree(BTreeNode* node, int level) {
    if (node) {
        printf("Level %d: ", level);
        for (int i = 0; i < node->num_keys; i++) {
            printf("%d ", node->keys[i]);
        }
        printf("\n");
        
        if (!node->is_leaf) {
            for (int i = 0; i <= node->num_keys; i++) {
                print_tree(node->children[i], level + 1);
            }
        }
    }
}

// Main function to test the B-tree implementation
int main() {
    BTreeNode* root = create_node(true);
    
    int keys[] = {10, 20, 5, 6, 12, 30, 7, 17, 3, 1, 8, 25, 27, 28, 35, 40, 45};
    int n = sizeof(keys)/sizeof(keys[0]);
    
    for (int i = 0; i < n; i++) {
        insert(&root, keys[i]);
        printf("Inserted %d:\n", keys[i]);
        print_tree(root, 0);
        printf("\n");
    }
    
    printf("Final tree:\n");
    print_tree(root, 0);
    printf("\n");
    
    // Delete some keys
    int keys_to_delete[] = {6, 13, 7, 4, 20, 17, 10};
    int m = sizeof(keys_to_delete)/sizeof(keys_to_delete[0]);
    
    for (int i = 0; i < m; i++) {
        printf("Deleting %d:\n", keys_to_delete[i]);
        delete_key(&root, keys_to_delete[i]);
        print_tree(root, 0);
        printf("\n");
    }
    
    printf("Final tree after deletions:\n");
    print_tree(root, 0);
    
    return 0;
}
```

## Explanation of the Implementation

1. **Node Structure**:
   - Each node contains an array of keys and an array of child pointers
   - `num_keys` tracks how many keys are currently in the node
   - `is_leaf` indicates whether the node is a leaf

2. **Insertion**:
   - The tree grows from the root when it becomes full
   - `insert_non_full` handles inserting into nodes that aren't full
   - `split_child` splits a full child node during insertion

3. **Deletion**:
   - `delete_from_subtree` handles the recursive deletion process
   - `fill` ensures a node has enough keys before descending
   - `borrow_from_left` and `borrow_from_right` handle key redistribution
   - `merge` combines two nodes when redistribution isn't possible

4. **Helper Functions**:
   - `get_predecessor` and `get_successor` find replacement keys for internal node deletions
   - `print_tree` displays the tree structure for visualization

This implementation maintains all B-tree properties during insertions and deletions, ensuring the tree remains balanced at all times. The minimum degree `t` is set to 3 in this example, but can be adjusted as needed.
