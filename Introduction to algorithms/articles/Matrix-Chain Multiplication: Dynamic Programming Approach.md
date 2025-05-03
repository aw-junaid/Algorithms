# Matrix-Chain Multiplication: Dynamic Programming Approach

## Introduction to the Problem

Matrix-chain multiplication is a classic optimization problem that seeks the most efficient way to multiply a sequence of matrices. The problem arises because matrix multiplication is associative (the order doesn't affect the final product) but the computational cost varies dramatically based on the parenthesization.

**Given:**
- A sequence of matrices A₁, A₂, ..., Aₙ
- The dimensions of matrix Aᵢ is p_{i-1} × pᵢ (so the matrices are compatible for multiplication)

**Goal:**
Determine the optimal parenthesization that minimizes the number of scalar multiplications.

## Key Concepts

### 1. Matrix Multiplication Cost
Multiplying an m×n matrix by an n×p matrix requires m×n×p scalar multiplications.

### 2. Optimal Substructure
The optimal solution to the problem can be constructed from optimal solutions to its subproblems.

### 3. Overlapping Subproblems
Different parenthesizations share common subproblems, making dynamic programming suitable.

## Approaches

### 1. Recursive Approach
- Direct recursive implementation has exponential time complexity O(2ⁿ)
- Highly inefficient due to repeated computations

### 2. Dynamic Programming Approach

#### a) Memoization (Top-Down)
- Recursive but stores solutions to subproblems
- Time complexity: O(n³)
- Space complexity: O(n²)

#### b) Tabulation (Bottom-Up)
- Solves smaller subproblems first
- Builds up to the solution for the full chain
- Time complexity: O(n³)
- Space complexity: O(n²)

## Algorithm Details

### Bottom-Up Approach Steps:
1. Create two n×n tables:
   - `m[i][j]` = minimum cost to compute Aᵢ...Aⱼ
   - `s[i][j]` = index of optimal split (for reconstructing the solution)

2. Initialize:
   - `m[i][i] = 0` for all i (cost of single matrix is 0)

3. For chain length l from 2 to n:
   - For all i from 1 to n-l+1:
     - j = i + l - 1
     - m[i][j] = ∞
     - For k from i to j-1:
       - q = m[i][k] + m[k+1][j] + p_{i-1}*p_k*p_j
       - If q < m[i][j]:
         - m[i][j] = q
         - s[i][j] = k

4. The solution is in m[1][n]

## C Implementation

```c
#include <stdio.h>
#include <limits.h>

// Function to print optimal parenthesization
void printOptimalParenthesis(int s[][100], int i, int j) {
    if (i == j) {
        printf("A%d", i);
    } else {
        printf("(");
        printOptimalParenthesis(s, i, s[i][j]);
        printOptimalParenthesis(s, s[i][j] + 1, j);
        printf(")");
    }
}

// Matrix Chain Multiplication function
void matrixChainOrder(int p[], int n) {
    int m[n][n];  // Minimum cost table
    int s[n][n];  // Split index table

    // Cost is zero when multiplying one matrix
    for (int i = 1; i < n; i++) {
        m[i][i] = 0;
    }

    // l is chain length
    for (int l = 2; l < n; l++) {
        for (int i = 1; i < n - l + 1; i++) {
            int j = i + l - 1;
            m[i][j] = INT_MAX;
            
            for (int k = i; k <= j - 1; k++) {
                int q = m[i][k] + m[k + 1][j] + p[i - 1] * p[k] * p[j];
                if (q < m[i][j]) {
                    m[i][j] = q;
                    s[i][j] = k;
                }
            }
        }
    }

    printf("Minimum number of multiplications is %d\n", m[1][n - 1]);
    printf("Optimal parenthesization: ");
    printOptimalParenthesis(s, 1, n - 1);
    printf("\n");
}

int main() {
    // Example: 4 matrices with dimensions:
    // A1: 10×20, A2: 20×30, A3: 30×40, A4: 40×30
    int arr[] = {10, 20, 30, 40, 30};
    int n = sizeof(arr) / sizeof(arr[0]);

    matrixChainOrder(arr, n);

    return 0;
}
```

## Explanation of the Code

1. **printOptimalParenthesis function**:
   - Recursively prints the optimal parenthesization using the split index table `s`

2. **matrixChainOrder function**:
   - Initializes the cost table `m` and split table `s`
   - Fills the tables using bottom-up dynamic programming
   - For each possible chain length, computes the minimum cost by evaluating all possible splits
   - Stores the minimum cost and the optimal split point

3. **Main function**:
   - Provides an example sequence of matrix dimensions
   - Calls the matrixChainOrder function to compute and display results

## Time and Space Complexity Analysis

- **Time Complexity**: O(n³) - Three nested loops each running up to n
- **Space Complexity**: O(n²) - Two n×n tables to store intermediate results

## Applications

Matrix-chain multiplication optimization is crucial in:
1. Computer graphics transformations
2. Scientific computing applications
3. Database query optimization
4. Compiler design for expression evaluation
5. Any application involving multiple matrix operations

## Example Walkthrough

For matrices with dimensions:
- A1: 10×20
- A2: 20×30
- A3: 30×40
- A4: 40×30

The optimal parenthesization is ((A1(A2A3))A4) with minimum multiplications = 18000.

This implementation provides a complete solution to the matrix-chain multiplication problem, demonstrating the power of dynamic programming for optimization problems with overlapping subproblems.
