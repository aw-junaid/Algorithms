# Rod Cutting Problem: Dynamic Programming Approach

## Introduction to the Problem

The rod cutting problem is a classic optimization problem that demonstrates the power of dynamic programming. Here's the problem statement:

**Given:** 
- A rod of length `n` inches
- A table of prices `p[i]` for `i = 1, 2, ..., n` where `p[i]` is the price for a rod piece of length `i`

**Goal:** 
Determine the maximum revenue `r[n]` obtainable by cutting up the rod and selling the pieces.

## Key Concepts

### 1. Optimal Substructure
The problem exhibits optimal substructure, meaning the optimal solution to the problem can be constructed from optimal solutions to its subproblems.

### 2. Overlapping Subproblems
When solving the problem recursively, we encounter the same subproblems repeatedly, making dynamic programming an efficient approach.

## Approaches

### 1. Recursive Approach (Top-Down)
This is a straightforward but inefficient solution with exponential time complexity O(2^n).

### 2. Dynamic Programming Approaches

#### a) Memoization (Top-Down with Memoization)
- Recursive approach but stores solutions to subproblems to avoid recomputation
- Time complexity: O(n²)
- Space complexity: O(n)

#### b) Tabulation (Bottom-Up)
- Solves smaller subproblems first and stores their solutions
- Builds up to the solution for length `n`
- Time complexity: O(n²)
- Space complexity: O(n)

## Algorithm Details

### Bottom-Up Approach Steps:
1. Create an array `r[0..n]` to store maximum revenues for rods of lengths 0 to n
2. Initialize `r[0] = 0` (revenue for rod of length 0)
3. For each length `j` from 1 to n:
   - Set the maximum revenue `q` to negative infinity
   - For each possible first cut `i` from 1 to j:
     - Calculate revenue as `p[i] + r[j-i]`
     - Update `q` if this revenue is greater than current `q`
   - Store `r[j] = q`
4. Return `r[n]`

### Extended Version (To also find the cuts)
We can extend the solution to also keep track of the optimal cuts.

## C Implementation

```c
#include <stdio.h>
#include <limits.h>

// Returns the maximum of two integers
int max(int a, int b) {
    return (a > b) ? a : b;
}

// Basic rod cutting implementation (returns maximum revenue only)
int rodCutting(int price[], int n) {
    int r[n+1];
    r[0] = 0;  // Base case: revenue for rod of length 0
    
    for (int j = 1; j <= n; j++) {
        int q = INT_MIN;
        for (int i = 1; i <= j; i++) {
            q = max(q, price[i-1] + r[j-i]);
        }
        r[j] = q;
    }
    
    return r[n];
}

// Extended rod cutting implementation (returns max revenue and prints cuts)
int rodCuttingExtended(int price[], int n) {
    int r[n+1];    // Maximum revenue for each length
    int s[n+1];    // Optimal size of the first piece to cut off
    
    r[0] = 0;      // Base case
    
    for (int j = 1; j <= n; j++) {
        int q = INT_MIN;
        for (int i = 1; i <= j; i++) {
            if (q < price[i-1] + r[j-i]) {
                q = price[i-1] + r[j-i];
                s[j] = i;
            }
        }
        r[j] = q;
    }
    
    // Print the optimal cuts
    printf("Optimal cutting for rod of length %d:\n", n);
    while (n > 0) {
        printf("%d ", s[n]);
        n = n - s[n];
    }
    printf("\n");
    
    return r[n];
}

int main() {
    // Example price table (for rods of length 1 to 10)
    int price[] = {1, 5, 8, 9, 10, 17, 17, 20, 24, 30};
    int n = sizeof(price)/sizeof(price[0]);
    
    printf("Basic version (max revenue only):\n");
    printf("Maximum revenue: %d\n\n", rodCutting(price, n));
    
    printf("Extended version (with cuts):\n");
    rodCuttingExtended(price, n);
    
    return 0;
}
```

## Explanation of the Code

1. **Basic Version (`rodCutting` function)**:
   - Initializes an array `r` to store maximum revenues for each length
   - Fills the array bottom-up by considering all possible first cuts
   - Returns the maximum revenue for the full rod length

2. **Extended Version (`rodCuttingExtended` function)**:
   - Similar to basic version but also maintains an array `s` to track the first optimal cut for each length
   - After computing the maximum revenue, it reconstructs the optimal cuts by following the `s` array
   - Prints the sequence of optimal cuts

3. **Main Function**:
   - Provides an example price table
   - Demonstrates both versions of the algorithm
   - The extended version shows both the maximum revenue and the actual cuts

## Time and Space Complexity Analysis

- **Time Complexity**: O(n²) - There are two nested loops, each running up to n
- **Space Complexity**: O(n) - We use two arrays of size n+1 to store intermediate results

## Applications

The rod cutting problem is a classic example that demonstrates:
1. Dynamic programming principles
2. Optimization problems with overlapping subproblems
3. Problems where greedy approaches fail to find the optimal solution

Similar problems include:
- Unbounded knapsack problem
- Coin change problem
- Various resource allocation problems

This implementation provides a solid foundation for understanding dynamic programming and can be adapted for similar optimization problems.
