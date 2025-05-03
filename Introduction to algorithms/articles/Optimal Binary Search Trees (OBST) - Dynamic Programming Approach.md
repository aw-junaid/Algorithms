# Optimal Binary Search Trees (OBST) - Dynamic Programming Approach

## Introduction to Optimal BST

An Optimal Binary Search Tree (OBST) is a binary search tree that provides the smallest possible search time for a given sequence of accesses (or probabilities of accesses) to its keys. The goal is to minimize the expected search cost.

## Problem Definition

Given:
- A sorted sequence of `n` distinct keys: `K = [k₁, k₂, ..., kₙ]`
- Probabilities of searching for each key: `P = [p₁, p₂, ..., pₙ]`
- Probabilities of searching for values not in the tree (dummy keys representing intervals between real keys): `Q = [q₀, q₁, ..., qₙ]`

We want to construct a binary search tree with minimal expected search cost.

## Expected Search Cost

The expected search cost of a BST is defined as:
```
E[search cost] = Σ (depth(kᵢ) + 1) * pᵢ + Σ depth(dᵢ) * qᵢ
```
where:
- `depth(kᵢ)` is the depth of key `kᵢ` in the tree
- `depth(dᵢ)` is the depth of dummy key `dᵢ`

## Dynamic Programming Approach

We use dynamic programming to solve this problem efficiently:

1. **Subproblems**: For each subtree containing keys `kᵢ` to `k�`, compute the optimal cost.

2. **Recurrence Relation**:
   - Let `e[i,j]` be the expected search cost of the optimal BST containing keys `kᵢ` to `kⱼ`
   - Let `w[i,j]` be the sum of probabilities for the subtree containing `kᵢ` to `kⱼ`:
     ```
     w[i,j] = q_{i-1} + Σ (pₖ + qₖ) for k from i to j
     ```
   - The recurrence is:
     ```
     e[i,j] = min_{i ≤ r ≤ j} (e[i,r-1] + e[r+1,j] + w[i,j])
     ```

3. **Base Cases**:
   - `e[i,i-1] = q_{i-1}` (empty tree with just dummy key `d_{i-1}`)

## Algorithm Implementation in C

Here's the complete implementation of the OBST algorithm in C:

```c
#include <stdio.h>
#include <limits.h>
#include <stdlib.h>

// Function to construct and print the optimal BST
void optimalBST(float p[], float q[], int n) {
    // Create weight, cost, and root tables
    float w[n+2][n+1], e[n+2][n+1];
    int root[n+1][n+1];
    
    // Initialize tables
    for (int i = 1; i <= n+1; i++) {
        e[i][i-1] = q[i-1];
        w[i][i-1] = q[i-1];
    }
    
    // Fill tables
    for (int l = 1; l <= n; l++) {
        for (int i = 1; i <= n - l + 1; i++) {
            int j = i + l - 1;
            e[i][j] = INT_MAX;
            w[i][j] = w[i][j-1] + p[j] + q[j];
            
            for (int r = i; r <= j; r++) {
                float temp = e[i][r-1] + e[r+1][j] + w[i][j];
                if (temp < e[i][j]) {
                    e[i][j] = temp;
                    root[i][j] = r;
                }
            }
        }
    }
    
    // Print results
    printf("Optimal BST Cost: %.2f\n", e[1][n]);
    printf("Root of the tree: k%d\n", root[1][n]);
    
    // (Optional: Add code to print the full tree structure)
}

int main() {
    // Example from CLRS book
    float p[] = {0, 0.15, 0.10, 0.05, 0.10, 0.20};
    float q[] = {0.05, 0.10, 0.05, 0.05, 0.05, 0.10};
    int n = sizeof(p)/sizeof(p[0]) - 1;
    
    optimalBST(p, q, n);
    
    return 0;
}
```

## Explanation of the Implementation

1. **Initialization**:
   - We create tables `w`, `e`, and `root` to store weights, expected costs, and root information.
   - The base cases are initialized where `e[i][i-1]` represents empty subtrees.

2. **Filling the Tables**:
   - We consider subtrees of increasing lengths (`l` from 1 to `n`).
   - For each possible subtree (from `i` to `j`), we:
     - Calculate the weight `w[i][j]` as the sum of all probabilities in that range
     - Try every possible root `r` between `i` and `j`
     - Select the root that gives the minimum expected cost

3. **Result Extraction**:
   - The optimal cost is found in `e[1][n]`
   - The root of the entire tree is in `root[1][n]`

## Time and Space Complexity

- **Time Complexity**: O(n³) due to the triple nested loops
- **Space Complexity**: O(n²) for storing the DP tables

## Example Walkthrough

For the input:
```
Keys: [ , k1, k2, k3, k4, k5]
p: [0, 0.15, 0.10, 0.05, 0.10, 0.20]
q: [0.05, 0.10, 0.05, 0.05, 0.05, 0.10]
```

The algorithm will:
1. Compute all possible subtrees
2. Find that the optimal root is k2
3. Calculate the minimal expected search cost as 2.75

## Extensions

To actually build the tree (not just compute the cost), you would:
1. Use the root table to recursively construct the tree
2. For each subtree `i` to `j`, the root is `root[i][j]`
3. Left subtree contains keys `i` to `root[i][j]-1`
4. Right subtree contains keys `root[i][j]+1` to `j`

This implementation provides the foundation for understanding and implementing optimal BST construction using dynamic programming.
