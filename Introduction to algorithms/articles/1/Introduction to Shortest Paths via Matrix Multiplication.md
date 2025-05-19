## Introduction to Shortest Paths via Matrix Multiplication

The matrix multiplication approach to solving the all-pairs shortest paths (APSP) problem is an interesting application of dynamic programming that uses matrix operations to compute shortest paths in a graph.

## Key Concepts

### 1. Problem Definition
Given a weighted, directed graph G = (V, E) with weight function w: E → ℝ, the all-pairs shortest paths problem asks us to find the shortest path (or its weight) between every pair of vertices.

### 2. Matrix Representation
We can represent the graph as a weight matrix W where:
- W[i][j] = weight of edge (i,j) if such edge exists
- W[i][j] = ∞ if no edge from i to j exists
- W[i][i] = 0 (distance from a vertex to itself is zero)

### 3. Dynamic Programming Approach
The solution is based on defining a series of matrices L^(m) where:
- L^(m)[i][j] = minimum weight of any path from i to j using at most m edges

The recurrence relation is:
L^(m)[i][j] = min(L^(m-1)[i][k] + W[k][j]) for all k ∈ V

### 4. Matrix Multiplication Analogy
This recurrence resembles matrix multiplication where:
- "Multiplication" becomes addition
- "Addition" becomes minimization

### 5. Algorithm Progression
- Start with L^(1) = W (paths with at most 1 edge)
- Compute L^(2), L^(3), ..., L^(n-1) where n = |V|
- L^(n-1) will contain the shortest paths (since no shortest path in a graph with no negative cycles can have more than n-1 edges)

### 6. Repeated Squaring
We can improve the algorithm by using repeated squaring:
- Compute L^(1), L^(2), L^(4), ..., until we reach or exceed L^(n-1)
- This reduces the time complexity from O(n⁴) to O(n³ log n)

## C Implementation

Here's a complete implementation of the algorithm in C:

```c
#include <stdio.h>
#include <limits.h>

#define V 4  // Number of vertices in the graph
#define INF INT_MAX  // Infinity value

// Function to print the solution matrix
void printSolution(int dist[][V]) {
    printf("The following matrix shows the shortest distances"
           " between every pair of vertices:\n");
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            if (dist[i][j] == INF)
                printf("%7s", "INF");
            else
                printf("%7d", dist[i][j]);
        }
        printf("\n");
    }
}

// Function to compute shortest paths using matrix multiplication
void shortestPaths(int graph[][V]) {
    int dist[V][V];  // The output matrix
    
    // Initialize the solution matrix same as input graph matrix
    for (int i = 0; i < V; i++)
        for (int j = 0; j < V; j++)
            dist[i][j] = graph[i][j];
    
    // Perform repeated squaring (log V times)
    for (int m = 2; m <= V-1; m *= 2) {
        // Temporary matrix to store current iteration
        int temp[V][V];
        for (int i = 0; i < V; i++) {
            for (int j = 0; j < V; j++) {
                temp[i][j] = INF;
                for (int k = 0; k < V; k++) {
                    // Avoid overflow
                    if (dist[i][k] != INF && graph[k][j] != INF && 
                        dist[i][k] + graph[k][j] < temp[i][j]) {
                        temp[i][j] = dist[i][k] + graph[k][j];
                    }
                }
            }
        }
        
        // Update dist matrix
        for (int i = 0; i < V; i++)
            for (int j = 0; j < V; j++)
                dist[i][j] = temp[i][j];
    }
    
    printSolution(dist);
}

int main() {
    /* Let us create the following weighted graph
            10
       (0)------->(3)
        |         /|\
      5 |          |
        |          | 1
       \|/         |
       (1)------->(2)
            3           */
    int graph[V][V] = { {0,   5,  INF, 10},
                        {INF, 0,   3, INF},
                        {INF, INF, 0,   1},
                        {INF, INF, INF, 0} };
    
    shortestPaths(graph);
    return 0;
}
```

## Explanation of the Implementation

1. **Initialization**: We start with the adjacency matrix representation of the graph.

2. **Matrix Multiplication**: The core of the algorithm is the triple-nested loop that performs the "matrix multiplication" operation:
   - Outer two loops iterate through each cell (i,j) in the matrix
   - Innermost loop finds the minimum path by considering all possible intermediate vertices k

3. **Repeated Squaring**: Instead of performing n-1 multiplications, we square the matrix each time (L^1, L^2, L^4, etc.), reducing the number of multiplications needed.

4. **Termination**: After ⌈log(n-1)⌉ multiplications, we have the final result matrix containing all-pairs shortest paths.

## Time and Space Complexity

- **Time Complexity**: O(V³ log V) - The repeated squaring approach reduces the number of matrix multiplications from V to log V
- **Space Complexity**: O(V²) - We need to store the distance matrices

## Advantages and Limitations

**Advantages**:
- Simple implementation using matrix operations
- Works well for dense graphs
- Can be parallelized effectively

**Limitations**:
- Slower than Floyd-Warshall for most practical cases (O(V³) vs O(V³ log V))
- Not as space-efficient as some other algorithms

This approach provides an interesting connection between graph algorithms and matrix operations, demonstrating how problems in one domain can sometimes be solved using techniques from another.
