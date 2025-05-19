# Floyd-Warshall Algorithm: Comprehensive Explanation and Implementation

The Floyd-Warshall algorithm is a dynamic programming approach for finding the shortest paths between all pairs of vertices in a weighted graph, which may include negative weight edges (but no negative cycles). It's named after Robert Floyd and Stephen Warshall.

## Key Characteristics

1. **All-pairs shortest path**: Finds shortest paths between every pair of vertices
2. **Handles negative weights**: Works with graphs containing negative weight edges
3. **Detects negative cycles**: Can identify if the graph contains negative weight cycles
4. **Time complexity**: O(V³) where V is the number of vertices
5. **Space complexity**: O(V²) for the distance matrix

## Algorithm Details

### Core Idea

The algorithm works by progressively improving an estimate on the shortest path between two vertices by considering each vertex as an intermediate point.

### Dynamic Programming Recurrence

The main recurrence relation is:
```
dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
```
where `k` is the intermediate vertex being considered.

### Steps

1. Initialize a distance matrix where:
   - `dist[i][j] = 0` if `i == j`
   - `dist[i][j] = weight of edge (i,j)` if edge exists
   - `dist[i][j] = INF` otherwise (INF is a very large number)

2. For each intermediate vertex `k` from 1 to V:
   - For each pair of vertices `i` and `j`:
     - If going through `k` provides a shorter path, update `dist[i][j]`

3. After all iterations, the distance matrix contains the shortest paths between all pairs

## Implementation in C

```c
#include <stdio.h>
#include <limits.h>

// Number of vertices in the graph
#define V 4

// Define INF as a very large value
#define INF 99999

// Function to print the solution matrix
void printSolution(int dist[][V]) {
    printf("The following matrix shows the shortest distances"
           " between every pair of vertices \n");
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

// Solves the all-pairs shortest path problem using Floyd-Warshall algorithm
void floydWarshall(int graph[][V]) {
    int dist[V][V], i, j, k;

    // Initialize the solution matrix same as input graph matrix
    for (i = 0; i < V; i++)
        for (j = 0; j < V; j++)
            dist[i][j] = graph[i][j];

    // Add all vertices one by one to the set of intermediate vertices
    for (k = 0; k < V; k++) {
        // Pick all vertices as source one by one
        for (i = 0; i < V; i++) {
            // Pick all vertices as destination for the above source
            for (j = 0; j < V; j++) {
                // If vertex k is on the shortest path from i to j,
                // then update the value of dist[i][j]
                if (dist[i][k] + dist[k][j] < dist[i][j])
                    dist[i][j] = dist[i][k] + dist[k][j];
            }
        }
    }

    // Check for negative weight cycles
    for (i = 0; i < V; i++)
        if (dist[i][i] < 0) {
            printf("Graph contains negative weight cycle\n");
            return;
        }

    // Print the shortest distance matrix
    printSolution(dist);
}

// Driver program to test above function
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
                        {INF, INF, INF, 0}
                      };

    // Print the solution
    floydWarshall(graph);
    return 0;
}
```

## Output Explanation

For the given graph, the output would be:

```
The following matrix shows the shortest distances between every pair of vertices 
      0      5      8      9
    INF      0      3      4
    INF    INF      0      1
    INF    INF    INF      0
```

This shows:
- Shortest path from 0 to 1 is 5
- Shortest path from 0 to 2 is 8 (0→1→2)
- Shortest path from 0 to 3 is 9 (0→1→2→3)
- And so on for other pairs

## Advantages

1. Works for both directed and undirected graphs
2. Handles negative weights (but not negative cycles)
3. Simple implementation with triple nested loops
4. Easy to reconstruct paths (with additional parent matrix)

## Limitations

1. O(V³) time complexity makes it unsuitable for very large graphs
2. Not as efficient as Dijkstra's for single-source problems
3. Requires O(V²) space which can be significant for large graphs

The Floyd-Warshall algorithm is particularly useful when you need to find shortest paths between all pairs of vertices in a graph, especially when the graph contains negative weight edges (without negative cycles).
