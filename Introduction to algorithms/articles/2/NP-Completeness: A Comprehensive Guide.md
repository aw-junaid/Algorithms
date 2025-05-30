# NP-Completeness: A Comprehensive Guide

## 1. Polynomial Time

**Polynomial time** refers to algorithms whose running time is bounded by a polynomial function of the input size. Formally, an algorithm runs in polynomial time if its time complexity is O(n^k) for some constant k, where n is the size of the input.

### Characteristics:
- Considered "efficient" in computational complexity theory
- Includes time complexities like O(n), O(n²), O(n³), etc.
- Contrasts with exponential time (O(2^n)) which grows much faster

### Example Polynomial Time Algorithm in C (Bubble Sort):

```c
#include <stdio.h>

void bubbleSort(int arr[], int n) {
    for (int i = 0; i < n-1; i++) {
        for (int j = 0; j < n-i-1; j++) {
            if (arr[j] > arr[j+1]) {
                // Swap arr[j] and arr[j+1]
                int temp = arr[j];
                arr[j] = arr[j+1];
                arr[j+1] = temp;
            }
        }
    }
}

int main() {
    int arr[] = {64, 34, 25, 12, 22, 11, 90};
    int n = sizeof(arr)/sizeof(arr[0]);
    bubbleSort(arr, n);
    printf("Sorted array: \n");
    for (int i=0; i < n; i++)
        printf("%d ", arr[i]);
    return 0;
}
```

## 2. Polynomial-Time Verification

**Polynomial-time verification** refers to the ability to verify a proposed solution to a problem in polynomial time, given some additional information (a certificate).

### Key Points:
- For a decision problem, given a potential solution (certificate), we can check if it's correct in polynomial time
- Doesn't necessarily mean we can find the solution in polynomial time
- Fundamental to the definition of NP problems

### Example: Hamiltonian Cycle Verification in C

```c
#include <stdio.h>
#include <stdbool.h>

#define V 5

bool isHamiltonianCycle(int graph[V][V], int path[V]) {
    // Check if each vertex appears exactly once
    for (int i = 0; i < V; i++) {
        for (int j = i+1; j < V; j++) {
            if (path[i] == path[j]) return false;
        }
    }
    
    // Check if edges exist between consecutive vertices
    for (int i = 0; i < V-1; i++) {
        if (graph[path[i]][path[i+1]] == 0) return false;
    }
    
    // Check if last vertex connects back to first
    if (graph[path[V-1]][path[0]] == 0) return false;
    
    return true;
}

int main() {
    int graph[V][V] = {
        {0, 1, 0, 1, 0},
        {1, 0, 1, 1, 1},
        {0, 1, 0, 0, 1},
        {1, 1, 0, 0, 1},
        {0, 1, 1, 1, 0}
    };
    
    int path[V] = {0, 1, 2, 4, 3};
    
    if (isHamiltonianCycle(graph, path))
        printf("The given path is a Hamiltonian cycle.\n");
    else
        printf("The given path is NOT a Hamiltonian cycle.\n");
    
    return 0;
}
```

## 3. NP-Completeness and Reducibility

### NP-Completeness:
- A problem is **NP-complete** if:
  1. It is in NP (solutions can be verified in polynomial time)
  2. Every problem in NP can be reduced to it in polynomial time (NP-hard)
- NP-complete problems are the "hardest" problems in NP
- If any NP-complete problem can be solved in polynomial time, then P = NP

### Reducibility:
- A problem A is polynomial-time reducible to problem B (A ≤ₚ B) if:
  - Any instance of A can be transformed into an instance of B in polynomial time
  - The answer to B's instance is the same as A's instance
- Used to prove NP-completeness by reducing known NP-complete problems to new problems

## 4. NP-Completeness Proofs

To prove a problem is NP-complete:
1. **Show the problem is in NP**: Demonstrate that given a certificate, you can verify a solution in polynomial time
2. **Show the problem is NP-hard**: Reduce a known NP-complete problem to your problem in polynomial time

### Example: Proving 3-SAT is NP-complete
1. 3-SAT is in NP: Given an assignment, we can verify it satisfies the formula in polynomial time
2. Reduce SAT to 3-SAT (showing 3-SAT is at least as hard as SAT)

## 5. NP-Complete Problems

Common NP-complete problems include:
- Boolean satisfiability problem (SAT)
- 3-SAT
- Hamiltonian path/cycle
- Traveling salesman problem (decision version)
- Vertex cover
- Clique problem
- Subset sum problem
- Graph coloring

### Example: Subset Sum Problem Implementation in C

```c
#include <stdio.h>
#include <stdbool.h>

bool isSubsetSum(int set[], int n, int sum) {
    // Base cases
    if (sum == 0) return true;
    if (n == 0 && sum != 0) return false;
    
    // If last element is greater than sum, ignore it
    if (set[n-1] > sum)
        return isSubsetSum(set, n-1, sum);
    
    // Check if sum can be obtained by either including or excluding last element
    return isSubsetSum(set, n-1, sum) || 
           isSubsetSum(set, n-1, sum - set[n-1]);
}

int main() {
    int set[] = {3, 34, 4, 12, 5, 2};
    int sum = 9;
    int n = sizeof(set)/sizeof(set[0]);
    
    if (isSubsetSum(set, n, sum))
        printf("Found a subset with given sum\n");
    else
        printf("No subset with given sum\n");
    
    return 0;
}
```
---
