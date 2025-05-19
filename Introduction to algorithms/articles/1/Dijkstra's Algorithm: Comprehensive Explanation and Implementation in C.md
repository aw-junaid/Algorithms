# Dijkstra's Algorithm: Comprehensive Explanation and Implementation in C

## Introduction to Dijkstra's Algorithm

Dijkstra's algorithm is a **greedy algorithm** developed by Dutch computer scientist Edsger W. Dijkstra in 1956. It solves the **single-source shortest path problem** for a graph with **non-negative edge weights**, producing a shortest path tree from a starting node to all other nodes in the graph.

## Key Concepts

1. **Purpose**: Finds the shortest paths from a source node to all other nodes in a weighted graph
2. **Graph Requirements**:
   - Can be directed or undirected
   - Must have non-negative edge weights
   - Can be cyclic or acyclic

## Algorithm Steps

1. **Initialization**:
   - Set distance to source node as 0
   - Set distance to all other nodes as infinity
   - Mark all nodes as unvisited

2. **Main Loop**:
   - Select the unvisited node with the smallest distance
   - For the current node, examine all unvisited neighbors
   - Calculate their tentative distances through the current node
   - If the calculated distance is less than the known distance, update it
   - Mark the current node as visited

3. **Termination**:
   - When all nodes have been visited (or when the smallest distance among unvisited nodes is infinity)
   - The algorithm terminates

## Time Complexity

- **Basic implementation**: O(V²) where V is number of vertices
- **With priority queue**: O(E + V log V) where E is number of edges

## Space Complexity

- O(V) for storing distances and predecessors
- Additional space for priority queue if used

## Limitations

1. Doesn't work with negative edge weights
2. Not suitable for graphs with negative cycles
3. For large graphs, the basic O(V²) implementation can be slow

## C Implementation

```c
#include <stdio.h>
#include <limits.h>
#include <stdbool.h>

#define V 6 // Number of vertices in the graph

// Function to find the vertex with minimum distance value
int minDistance(int dist[], bool sptSet[]) {
    int min = INT_MAX, min_index;
    
    for (int v = 0; v < V; v++) {
        if (sptSet[v] == false && dist[v] <= min) {
            min = dist[v];
            min_index = v;
        }
    }
    
    return min_index;
}

// Function to print the constructed distance array
void printSolution(int dist[]) {
    printf("Vertex \t Distance from Source\n");
    for (int i = 0; i < V; i++) {
        printf("%d \t %d\n", i, dist[i]);
    }
}

// Dijkstra's algorithm implementation
void dijkstra(int graph[V][V], int src) {
    int dist[V];     // The output array. dist[i] will hold the shortest distance from src to i
    bool sptSet[V];  // sptSet[i] will be true if vertex i is included in shortest path tree
    
    // Initialize all distances as INFINITE and sptSet[] as false
    for (int i = 0; i < V; i++) {
        dist[i] = INT_MAX;
        sptSet[i] = false;
    }
    
    // Distance of source vertex from itself is always 0
    dist[src] = 0;
    
    // Find shortest path for all vertices
    for (int count = 0; count < V - 1; count++) {
        // Pick the minimum distance vertex from the set of vertices not yet processed
        int u = minDistance(dist, sptSet);
        
        // Mark the picked vertex as processed
        sptSet[u] = true;
        
        // Update dist value of the adjacent vertices of the picked vertex
        for (int v = 0; v < V; v++) {
            // Update dist[v] only if:
            // 1. It's not in sptSet
            // 2. There's an edge from u to v
            // 3. Total weight of path from src to v through u is smaller than current value of dist[v]
            if (!sptSet[v] && graph[u][v] && dist[u] != INT_MAX && dist[u] + graph[u][v] < dist[v]) {
                dist[v] = dist[u] + graph[u][v];
            }
        }
    }
    
    // Print the constructed distance array
    printSolution(dist);
}

int main() {
    // Example graph represented as adjacency matrix
    int graph[V][V] = {
        {0, 4, 0, 0, 0, 0},
        {4, 0, 8, 0, 0, 0},
        {0, 8, 0, 7, 0, 4},
        {0, 0, 7, 0, 9, 14},
        {0, 0, 0, 9, 0, 10},
        {0, 0, 4, 14, 10, 0}
    };
    
    dijkstra(graph, 0); // Run Dijkstra's algorithm with source vertex 0
    
    return 0;
}
```

## Explanation of the C Code

1. **Graph Representation**: The graph is represented as an adjacency matrix where `graph[i][j]` represents the weight of the edge from vertex i to vertex j.

2. **minDistance Function**: Finds the vertex with the minimum distance value from the set of vertices not yet included in the shortest path tree.

3. **printSolution Function**: Prints the final distances from the source to all vertices.

4. **dijkstra Function**:
   - Initializes distance array with INFINITE and visited array as false
   - Sets distance of source vertex to 0
   - For each vertex, finds the minimum distance vertex, updates its neighbors' distances if a shorter path is found
   - Continues until all vertices are processed

5. **main Function**: Creates a sample graph and runs Dijkstra's algorithm on it.

## Sample Output

For the given graph, the output would be:
```
Vertex   Distance from Source
0        0
1        4
2        12
3        19
4        21
5        16
```

This indicates the shortest distances from vertex 0 to all other vertices in the graph.
