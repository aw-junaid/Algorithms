## 1. Key Properties of Shortest Paths

### a) Optimal Substructure Property
**Statement**: Any subpath of a shortest path is itself a shortest path.

**Proof**:
1. Consider a shortest path p from vertex u to v.
2. Let this path go through vertex w, so we have u → ... → w → ... → v.
3. If there existed a shorter path from w to v than the subpath in p, we could substitute it into p to get an even shorter path from u to v.
4. This contradicts our assumption that p is the shortest path.
5. Therefore, the subpath from w to v must itself be a shortest path.

### b) Triangle Inequality
**Statement**: For all vertices u, v, w ∈ V, δ(u,v) ≤ δ(u,w) + δ(w,v) where δ(a,b) is the shortest path distance from a to b.

**Proof**:
1. The shortest path from u to v cannot be longer than the path that goes through w.
2. If it were, we could just take the path u → w → v which would be shorter, contradicting the definition of δ(u,v).

### c) No Negative-weight Cycles
**Statement**: If a graph contains a negative-weight cycle reachable from the source, shortest paths are not well-defined.

**Proof**:
1. Suppose there's a negative-weight cycle reachable from source s.
2. For any vertex v on the cycle, we can always find a shorter path by going around the cycle one more time.
3. Thus, we can make the path arbitrarily short (negative infinity), meaning no finite shortest path exists.

### d) Relaxation Property
**Statement**: Relaxation maintains upper bounds on shortest-path distances.

**Proof**:
1. Initially, d[v] = ∞ for all v ≠ s, and d[s] = 0.
2. When we relax an edge (u,v), we set d[v] = min(d[v], d[u] + w(u,v)).
3. Since d[u] is an upper bound on δ(s,u) and w(u,v) is the exact weight, d[v] remains an upper bound on δ(s,v).

## 2. Dijkstra's Algorithm Implementation in C

Here's a complete implementation of Dijkstra's algorithm for finding shortest paths from a single source:

```c
#include <stdio.h>
#include <limits.h>
#include <stdbool.h>

#define V 9 // Number of vertices in the graph

// Utility function to find the vertex with minimum distance value
int minDistance(int dist[], bool sptSet[]) {
    int min = INT_MAX, min_index;
    
    for (int v = 0; v < V; v++) {
        if (!sptSet[v] && dist[v] <= min) {
            min = dist[v];
            min_index = v;
        }
    }
    
    return min_index;
}

// Utility function to print the distance array
void printSolution(int dist[]) {
    printf("Vertex \t Distance from Source\n");
    for (int i = 0; i < V; i++) {
        printf("%d \t %d\n", i, dist[i]);
    }
}

// Dijkstra's algorithm implementation for adjacency matrix representation
void dijkstra(int graph[V][V], int src) {
    int dist[V];     // The output array with shortest distances
    bool sptSet[V];  // sptSet[i] is true if vertex i is included in shortest path tree
    
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
            // 3. Total weight of path from src to v through u is smaller than current dist[v]
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
        {0, 4, 0, 0, 0, 0, 0, 8, 0},
        {4, 0, 8, 0, 0, 0, 0, 11, 0},
        {0, 8, 0, 7, 0, 4, 0, 0, 2},
        {0, 0, 7, 0, 9, 14, 0, 0, 0},
        {0, 0, 0, 9, 0, 10, 0, 0, 0},
        {0, 0, 4, 14, 10, 0, 2, 0, 0},
        {0, 0, 0, 0, 0, 2, 0, 1, 6},
        {8, 11, 0, 0, 0, 0, 1, 0, 7},
        {0, 0, 2, 0, 0, 0, 6, 7, 0}
    };
    
    dijkstra(graph, 0);
    
    return 0;
}
```

## 3. Explanation of Dijkstra's Algorithm Implementation

1. **Initialization**:
   - `dist[]` array stores the shortest distance from source to each vertex (initialized to ∞ except source which is 0)
   - `sptSet[]` (Shortest Path Tree Set) keeps track of vertices included in the shortest path tree

2. **Main Loop**:
   - Select the vertex `u` with minimum distance value that's not yet in `sptSet`
   - Add `u` to `sptSet`
   - Update distance values of adjacent vertices of `u` through a process called relaxation

3. **Relaxation**:
   - For each adjacent vertex `v` of `u`, if the current distance to `v` is greater than the distance to `u` plus the edge weight from `u` to `v`, update the distance to `v`

4. **Termination**:
   - The algorithm terminates when all vertices are included in `sptSet`
   - The `dist[]` array now contains the shortest distances from the source to all other vertices

## 4. Time Complexity Analysis

The time complexity of this implementation is O(V²) where V is the number of vertices. This can be improved to O(E + V log V) using a min-heap for the priority queue, but the above implementation uses a simpler array-based approach for clarity.

The algorithm works for graphs with non-negative edge weights only, as guaranteed by the properties we proved earlier.
