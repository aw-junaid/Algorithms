# Minimum Spanning Trees: Growing a Minimum Spanning Tree

## Introduction to Minimum Spanning Trees (MST)

A Minimum Spanning Tree (MST) is a subset of the edges of a connected, undirected graph that connects all the vertices together, without any cycles, and with the minimum possible total edge weight. There are two main algorithms for finding MSTs: Prim's algorithm and Kruskal's algorithm. Here, I'll focus on Prim's algorithm, which grows the MST one vertex at a time.

## Prim's Algorithm Explained

Prim's algorithm is a greedy algorithm that grows the MST by adding the cheapest possible edge from the tree to a vertex not yet in the tree. Here's how it works:

1. **Initialization**: Start with any vertex as the initial MST.
2. **Growing the tree**: At each step, add the cheapest edge that connects a vertex in the MST to a vertex outside the MST.
3. **Termination**: Continue until all vertices are included in the MST.

### Detailed Steps:

1. Create a set `mstSet` to keep track of vertices already included in MST.
2. Assign a key value to all vertices (initially INFINITE) and set key of first vertex to 0.
3. While `mstSet` doesn't include all vertices:
   a. Pick vertex `u` not in `mstSet` with minimum key value
   b. Add `u` to `mstSet`
   c. Update key values of all adjacent vertices of `u`:
      - For each adjacent vertex `v`, if weight of edge `u-v` is less than current key of `v`, update key of `v` to this weight

## Implementation in C

Here's a complete implementation of Prim's algorithm in C:

```c
#include <stdio.h>
#include <stdbool.h>
#include <limits.h>

#define V 5  // Number of vertices in the graph

// Function to find the vertex with minimum key value
int minKey(int key[], bool mstSet[]) {
    int min = INT_MAX, min_index;
    
    for (int v = 0; v < V; v++) {
        if (mstSet[v] == false && key[v] < min) {
            min = key[v];
            min_index = v;
        }
    }
    
    return min_index;
}

// Function to print the constructed MST
void printMST(int parent[], int graph[V][V]) {
    printf("Edge \tWeight\n");
    for (int i = 1; i < V; i++) {
        printf("%d - %d \t%d \n", parent[i], i, graph[i][parent[i]]);
    }
}

// Function to construct and print MST using Prim's algorithm
void primMST(int graph[V][V]) {
    int parent[V];      // Array to store constructed MST
    int key[V];         // Key values used to pick minimum weight edge
    bool mstSet[V];     // To represent set of vertices included in MST
    
    // Initialize all keys as INFINITE and mstSet[] as false
    for (int i = 0; i < V; i++) {
        key[i] = INT_MAX;
        mstSet[i] = false;
    }
    
    // Always include first vertex in MST
    key[0] = 0;         // Make key 0 so this vertex is picked first
    parent[0] = -1;     // First node is always root of MST
    
    // The MST will have V vertices
    for (int count = 0; count < V - 1; count++) {
        // Pick the minimum key vertex from vertices not yet in MST
        int u = minKey(key, mstSet);
        
        // Add the picked vertex to the MST set
        mstSet[u] = true;
        
        // Update key value and parent index of adjacent vertices
        for (int v = 0; v < V; v++) {
            // Update key only if graph[u][v] is not 0 (adjacent), 
            // v is not in mstSet, and graph[u][v] is smaller than current key[v]
            if (graph[u][v] && mstSet[v] == false && graph[u][v] < key[v]) {
                parent[v] = u;
                key[v] = graph[u][v];
            }
        }
    }
    
    // Print the constructed MST
    printMST(parent, graph);
}

int main() {
    /* Example graph represented as adjacency matrix
          2    3
      0----1----2
      |   / \   |
     6| 8/   \5 |7
      | /     \ |
      3---------4
          9
    */
    int graph[V][V] = {
        {0, 2, 0, 6, 0},
        {2, 0, 3, 8, 5},
        {0, 3, 0, 0, 7},
        {6, 8, 0, 0, 9},
        {0, 5, 7, 9, 0}
    };
    
    primMST(graph);
    
    return 0;
}
```

## Explanation of the Code

1. **Graph Representation**: The graph is represented as an adjacency matrix where `graph[i][j]` represents the weight of the edge between vertex i and j (0 means no edge).

2. **Key Arrays**:
   - `key[]`: Stores the minimum weight edge to connect each vertex to the MST
   - `mstSet[]`: Tracks which vertices are already in the MST
   - `parent[]`: Stores the parent of each vertex in the MST

3. **minKey() Function**: Finds the vertex with the minimum key value from vertices not yet in the MST.

4. **Main Algorithm**:
   - Initialize all keys as infinite and mstSet as false
   - Start with vertex 0 by setting its key to 0
   - For each vertex, find the minimum key vertex not in MST, add it to MST
   - Update keys of adjacent vertices if a smaller weight edge is found

5. **Output**: Prints the edges of the MST along with their weights.

## Time Complexity

The time complexity of Prim's algorithm is O(V²) for this adjacency matrix implementation. It can be improved to O(E log V) using a min-heap and adjacency list representation.

## Applications of MST

- Network design (telephone, electrical, hydraulic, TV cable)
- Approximation algorithms for NP-hard problems
- Cluster analysis
- Image segmentation
- Handwriting recognition
