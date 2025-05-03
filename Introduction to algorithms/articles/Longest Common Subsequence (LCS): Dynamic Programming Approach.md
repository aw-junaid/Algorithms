# Longest Common Subsequence (LCS): Dynamic Programming Approach

## Introduction to the Problem

The Longest Common Subsequence (LCS) problem is a classic computer science problem that finds applications in diverse fields from bioinformatics (DNA sequence alignment) to version control systems (diff algorithms).

**Problem Statement:**
Given two sequences X and Y, find the length of the longest subsequence present in both of them. A subsequence is a sequence that appears in the same relative order but not necessarily contiguous.

**Example:**
- X: "ABCBDAB"
- Y: "BDCABA"
- LCS: "BCBA" (length 4)

## Key Concepts

### 1. Optimal Substructure
The LCS problem has an optimal substructure property, meaning the optimal solution can be constructed from optimal solutions to its subproblems.

### 2. Overlapping Subproblems
When solved recursively, the problem contains many overlapping subproblems that are solved repeatedly, making it ideal for dynamic programming.

## Recursive Formulation

Let X[0..m-1] and Y[0..n-1] be two sequences of lengths m and n respectively.

The recursive solution considers two cases:
1. **Last characters match**: LCS(X[0..m-1], Y[0..n-1]) = 1 + LCS(X[0..m-2], Y[0..n-2])
2. **Last characters don't match**: LCS(X[0..m-1], Y[0..n-1]) = max(LCS(X[0..m-2], Y[0..n-1]), LCS(X[0..m-1], Y[0..n-2]))

## Dynamic Programming Approach

### 1. Tabulation (Bottom-Up)
We construct a 2D array dp[m+1][n+1] where:
- dp[i][j] = length of LCS of X[0..i-1] and Y[0..j-1]
- dp[0][j] = 0 and dp[i][0] = 0 (base cases)
- Fill the table using the recursive relations

### 2. Time and Space Complexity
- Time Complexity: O(mn)
- Space Complexity: O(mn) (can be optimized to O(min(m,n)))

## C Implementation

```c
#include <stdio.h>
#include <string.h>

// Utility function to find maximum of two integers
int max(int a, int b) {
    return (a > b) ? a : b;
}

// Returns length of LCS for X[0..m-1], Y[0..n-1]
int lcs_length(char *X, char *Y, int m, int n) {
    int dp[m+1][n+1];
    int i, j;
    
    // Build dp table in bottom-up fashion
    for (i = 0; i <= m; i++) {
        for (j = 0; j <= n; j++) {
            if (i == 0 || j == 0)
                dp[i][j] = 0;
            else if (X[i-1] == Y[j-1])
                dp[i][j] = dp[i-1][j-1] + 1;
            else
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
        }
    }
    return dp[m][n];
}

// Function to print the LCS
void print_lcs(char *X, char *Y, int m, int n) {
    int dp[m+1][n+1];
    int i, j;
    
    // Build dp table
    for (i = 0; i <= m; i++) {
        for (j = 0; j <= n; j++) {
            if (i == 0 || j == 0)
                dp[i][j] = 0;
            else if (X[i-1] == Y[j-1])
                dp[i][j] = dp[i-1][j-1] + 1;
            else
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
        }
    }
    
    // Following code is used to print LCS
    int index = dp[m][n];
    char lcs[index+1];
    lcs[index] = '\0'; // Terminate the string
    
    i = m, j = n;
    while (i > 0 && j > 0) {
        // If current characters match, they are part of LCS
        if (X[i-1] == Y[j-1]) {
            lcs[index-1] = X[i-1];
            i--; j--; index--;
        }
        // If not, find the larger of two and go in that direction
        else if (dp[i-1][j] > dp[i][j-1])
            i--;
        else
            j--;
    }
    
    printf("LCS: %s\n", lcs);
}

int main() {
    char X[] = "ABCBDAB";
    char Y[] = "BDCABA";
    int m = strlen(X);
    int n = strlen(Y);
    
    printf("Length of LCS is %d\n", lcs_length(X, Y, m, n));
    print_lcs(X, Y, m, n);
    
    return 0;
}
```

## Explanation of the Code

1. **lcs_length function**:
   - Creates a DP table of size (m+1)×(n+1)
   - Fills the table using bottom-up approach
   - Returns the value in dp[m][n] which is the LCS length

2. **print_lcs function**:
   - First builds the DP table same as lcs_length
   - Then traces back from dp[m][n] to construct the actual LCS string
   - Uses the DP table to determine the path of matched characters

3. **main function**:
   - Defines two input strings
   - Calls lcs_length to get the length of LCS
   - Calls print_lcs to print the actual subsequence

## Space Optimization

The space complexity can be reduced from O(mn) to O(min(m,n)) by using two 1D arrays instead of a 2D array:

```c
int lcs_optimized(char *X, char *Y, int m, int n) {
    int dp[2][n+1];
    int i, j;
    
    for (i = 0; i <= m; i++) {
        for (j = 0; j <= n; j++) {
            if (i == 0 || j == 0)
                dp[i%2][j] = 0;
            else if (X[i-1] == Y[j-1])
                dp[i%2][j] = dp[(i-1)%2][j-1] + 1;
            else
                dp[i%2][j] = max(dp[(i-1)%2][j], dp[i%2][j-1]);
        }
    }
    return dp[m%2][n];
}
```

## Applications of LCS

1. **Version Control Systems**: Used in diff tools to compare files
2. **Bioinformatics**: DNA sequence alignment
3. **Plagiarism Detection**: Comparing documents
4. **Data Comparison**: Database synchronization
5. **Speech Recognition**: Matching spoken words with text

## Variations of LCS Problem

1. **Longest Common Substring**: Contiguous characters
2. **Shortest Common Supersequence**
3. **Edit Distance**: Minimum operations to convert one string to another
4. **Multiple Sequence Alignment**: LCS for more than two strings

## Performance Analysis

For two strings of lengths m and n:
- **Time Complexity**: O(mn) - We fill a table of size m×n
- **Space Complexity**: 
  - Basic version: O(mn)
  - Optimized version: O(min(m,n))

This implementation provides a complete solution to the LCS problem, demonstrating both the length calculation and the actual subsequence reconstruction. The space-optimized version shows how to reduce memory usage while maintaining the same time complexity.
