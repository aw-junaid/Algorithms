# Van Emde Boas Trees: A Detailed Explanation

## Introduction to Van Emde Boas Trees

The van Emde Boas tree (or vEB tree) is a tree data structure that implements an associative array with integer keys. It was invented by Peter van Emde Boas in 1975 and provides efficient operations for priority queue operations (insert, delete, search, minimum, maximum, successor, predecessor) with time complexity O(log log M) where M is the size of the universe (the range of possible keys).

## Key Properties

- **Fast operations**: All operations run in O(log log M) time
- **Space complexity**: O(M) space (can be improved with hash tables)
- **Perfect for integer keys**: Especially useful when keys are bounded
- **Recursive structure**: Uses a hierarchical decomposition of the universe

## Preliminary Approaches

Before diving into vEB trees, let's look at simpler approaches that lead to its design:

### 1. Bit Vector Approach

For a universe of size M, we can maintain a bit vector (array of bits) where:
- Set bit at position x means x is in the set
- Operations:
  - Insert/Delete: O(1) - just flip the bit
  - Search: O(1) - check the bit
  - Min/Max: O(M) - scan the array
  - Successor/Predecessor: O(M) - scan from current position

### 2. Binary Search Tree Approach

- Standard BST operations are O(log n) where n is number of elements
- But min/max/successor/predecessor are still O(log n)
- Not as fast as we want for bounded universes

### 3. Superimposed Binary Trees

This leads to the idea of vEB trees - combining the benefits of both approaches with a recursive structure.

## Van Emde Boas Tree Structure

A vEB tree for a universe of size M = 2^m has:

1. **Summary**: A vEB tree of size √M (2^(m/2)) that keeps track of which clusters are non-empty
2. **Clusters**: An array of √M vEB trees, each of size √M
3. **Min**: The minimum element in the tree (stored separately for efficiency)
4. **Max**: The maximum element in the tree (stored separately)

### Recursive Structure Breakdown

For a universe size M = 2^m:
- Split the bits of each key into two parts:
  - High-order ⌈m/2⌉ bits (cluster number)
  - Low-order ⌊m/2⌋ bits (position within cluster)
- This creates a tree where each node has √M children

## Operations

All operations have O(log log M) time complexity:

1. **Insert(x)**:
   - If tree is empty, set min and max to x
   - If x < min, swap x with min
   - Recursively insert into appropriate cluster
   - Update summary if cluster was empty

2. **Delete(x)**:
   - Handle base cases (only one element, x is min or max)
   - If x is min, find new min from clusters
   - Recursively delete from appropriate cluster
   - Update summary if cluster becomes empty

3. **Search(x)**:
   - Check if x equals min or max
   - Otherwise, search in appropriate cluster

4. **Successor(x)**:
   - Check if x < min (return min)
   - Check within current cluster for successor
   - If none, find next non-empty cluster in summary

5. **Predecessor(x)**:
   - Similar to successor but in reverse

6. **Minimum()/Maximum()**:
   - Directly return min/max fields

## Implementation in C

Here's a complete implementation of a van Emde Boas tree in C:

```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <stdbool.h>

typedef struct vEB_tree {
    int universe_size;  // Size of the universe (M)
    int min;            // Minimum element in the tree
    int max;            // Maximum element in the tree
    struct vEB_tree *summary;  // Summary vEB tree
    struct vEB_tree **clusters; // Array of cluster vEB trees
} vEB_tree;

// Helper function to calculate square root upper bound
int upper_sqrt(int M) {
    return (int)pow(2, ceil(log2(M)/2));
}

// Helper function to calculate square root lower bound
int lower_sqrt(int M) {
    return (int)pow(2, floor(log2(M)/2));
}

// Create a new vEB tree
vEB_tree* create_vEB_tree(int M) {
    vEB_tree *tree = (vEB_tree*)malloc(sizeof(vEB_tree));
    tree->universe_size = M;
    tree->min = -1;
    tree->max = -1;
    
    if (M <= 2) {
        tree->summary = NULL;
        tree->clusters = NULL;
    } else {
        int upper_M = upper_sqrt(M);
        int lower_M = lower_sqrt(M);
        
        tree->summary = create_vEB_tree(upper_M);
        tree->clusters = (vEB_tree**)malloc(sizeof(vEB_tree*) * upper_M);
        
        for (int i = 0; i < upper_M; i++) {
            tree->clusters[i] = create_vEB_tree(lower_M);
        }
    }
    
    return tree;
}

// High bits of x (cluster number)
int high(vEB_tree *tree, int x) {
    int lower_M = lower_sqrt(tree->universe_size);
    return x / lower_M;
}

// Low bits of x (position within cluster)
int low(vEB_tree *tree, int x) {
    int lower_M = lower_sqrt(tree->universe_size);
    return x % lower_M;
}

// Reconstruct x from cluster and position
int index(vEB_tree *tree, int cluster, int position) {
    int lower_M = lower_sqrt(tree->universe_size);
    return cluster * lower_M + position;
}

// Insert an element into the vEB tree
void vEB_insert(vEB_tree *tree, int x) {
    if (tree->min == -1) {
        // Tree is empty
        tree->min = tree->max = x;
        return;
    }
    
    if (x < tree->min) {
        // Swap x with min (min is always in the tree without being in clusters)
        int temp = x;
        x = tree->min;
        tree->min = temp;
    }
    
    if (tree->universe_size > 2) {
        int cluster = high(tree, x);
        int position = low(tree, x);
        
        if (tree->clusters[cluster]->min == -1) {
            // Cluster is empty, insert into summary
            vEB_insert(tree->summary, cluster);
            // Initialize min and max for the cluster
            tree->clusters[cluster]->min = tree->clusters[cluster]->max = position;
        } else {
            // Recursively insert into the cluster
            vEB_insert(tree->clusters[cluster], position);
        }
    }
    
    if (x > tree->max) {
        tree->max = x;
    }
}

// Delete an element from the vEB tree
void vEB_delete(vEB_tree *tree, int x) {
    if (tree->min == tree->max) {
        // Only one element in the tree
        tree->min = tree->max = -1;
        return;
    }
    
    if (tree->universe_size == 2) {
        // Base case
        if (x == 0) {
            tree->min = 1;
        } else {
            tree->min = 0;
        }
        tree->max = tree->min;
        return;
    }
    
    if (x == tree->min) {
        // Find the new min
        int first_cluster = tree->summary->min;
        x = index(tree, first_cluster, tree->clusters[first_cluster]->min);
        tree->min = x;
    }
    
    // Now delete x from its cluster
    int cluster = high(tree, x);
    int position = low(tree, x);
    vEB_delete(tree->clusters[cluster], position);
    
    if (tree->clusters[cluster]->min == -1) {
        // Cluster is now empty, delete from summary
        vEB_delete(tree->summary, cluster);
        
        if (x == tree->max) {
            // Update max
            int summary_max = tree->summary->max;
            if (summary_max == -1) {
                tree->max = tree->min;
            } else {
                tree->max = index(tree, summary_max, tree->clusters[summary_max]->max);
            }
        }
    } else if (x == tree->max) {
        // Update max within the same cluster
        tree->max = index(tree, cluster, tree->clusters[cluster]->max);
    }
}

// Search for an element in the vEB tree
bool vEB_search(vEB_tree *tree, int x) {
    if (x == tree->min || x == tree->max) {
        return true;
    }
    
    if (tree->universe_size == 2) {
        return false;
    }
    
    return vEB_search(tree->clusters[high(tree, x)], low(tree, x));
}

// Find the successor of x
int vEB_successor(vEB_tree *tree, int x) {
    if (tree->universe_size == 2) {
        if (x == 0 && tree->max == 1) {
            return 1;
        } else {
            return -1;
        }
    }
    
    if (tree->min != -1 && x < tree->min) {
        return tree->min;
    }
    
    int cluster = high(tree, x);
    int position = low(tree, x);
    int max_in_cluster = tree->clusters[cluster]->max;
    
    if (max_in_cluster != -1 && position < max_in_cluster) {
        // Successor is in the same cluster
        int offset = vEB_successor(tree->clusters[cluster], position);
        return index(tree, cluster, offset);
    } else {
        // Find next non-empty cluster
        int successor_cluster = vEB_successor(tree->summary, cluster);
        if (successor_cluster == -1) {
            return -1;
        } else {
            int offset = tree->clusters[successor_cluster]->min;
            return index(tree, successor_cluster, offset);
        }
    }
}

// Find the predecessor of x
int vEB_predecessor(vEB_tree *tree, int x) {
    if (tree->universe_size == 2) {
        if (x == 1 && tree->min == 0) {
            return 0;
        } else {
            return -1;
        }
    }
    
    if (tree->max != -1 && x > tree->max) {
        return tree->max;
    }
    
    int cluster = high(tree, x);
    int position = low(tree, x);
    int min_in_cluster = tree->clusters[cluster]->min;
    
    if (min_in_cluster != -1 && position > min_in_cluster) {
        // Predecessor is in the same cluster
        int offset = vEB_predecessor(tree->clusters[cluster], position);
        return index(tree, cluster, offset);
    } else {
        // Find previous non-empty cluster
        int predecessor_cluster = vEB_predecessor(tree->summary, cluster);
        if (predecessor_cluster == -1) {
            if (tree->min != -1 && x > tree->min) {
                return tree->min;
            } else {
                return -1;
            }
        } else {
            int offset = tree->clusters[predecessor_cluster]->max;
            return index(tree, predecessor_cluster, offset);
        }
    }
}

// Get minimum element
int vEB_min(vEB_tree *tree) {
    return tree->min;
}

// Get maximum element
int vEB_max(vEB_tree *tree) {
    return tree->max;
}

// Free memory allocated for the vEB tree
void free_vEB_tree(vEB_tree *tree) {
    if (tree->universe_size > 2) {
        free_vEB_tree(tree->summary);
        for (int i = 0; i < upper_sqrt(tree->universe_size); i++) {
            free_vEB_tree(tree->clusters[i]);
        }
        free(tree->clusters);
    }
    free(tree);
}

// Example usage
int main() {
    int universe_size = 16; // Must be a power of 2
    vEB_tree *tree = create_vEB_tree(universe_size);
    
    printf("Inserting 2, 3, 4, 5, 7, 14, 15\n");
    vEB_insert(tree, 2);
    vEB_insert(tree, 3);
    vEB_insert(tree, 4);
    vEB_insert(tree, 5);
    vEB_insert(tree, 7);
    vEB_insert(tree, 14);
    vEB_insert(tree, 15);
    
    printf("Minimum: %d\n", vEB_min(tree));
    printf("Maximum: %d\n", vEB_max(tree));
    
    printf("Search for 5: %s\n", vEB_search(tree, 5) ? "Found" : "Not found");
    printf("Search for 6: %s\n", vEB_search(tree, 6) ? "Found" : "Not found");
    
    printf("Successor of 5: %d\n", vEB_successor(tree, 5));
    printf("Predecessor of 7: %d\n", vEB_predecessor(tree, 7));
    
    printf("Deleting 5\n");
    vEB_delete(tree, 5);
    printf("Search for 5 after deletion: %s\n", vEB_search(tree, 5) ? "Found" : "Not found");
    printf("Successor of 4 after deletion: %d\n", vEB_successor(tree, 4));
    
    free_vEB_tree(tree);
    return 0;
}
```

## Analysis

### Time Complexity

All operations (insert, delete, search, min, max, successor, predecessor) run in O(log log M) time where M is the universe size. This is because:

1. Each operation makes at most one recursive call to a vEB tree of size √M
2. The recurrence relation is T(M) = T(√M) + O(1)
3. Solving this gives T(M) = O(log log M)

### Space Complexity

The space complexity is O(M) because:

1. Each node stores √M pointers to clusters
2. The total space can be described by S(M) = (√M + 1) * S(√M) + O(1)
3. This results in O(M) space

### Practical Considerations

1. **Memory Usage**: The O(M) space can be prohibitive for large universes
2. **Alternative Implementations**: Can use hash tables to reduce space to O(n) where n is number of elements
3. **Cache Performance**: The recursive structure may have poor cache locality
4. **Best Use Cases**: When M is reasonable (e.g., 2^16 or 2^32) and operations need to be very fast


