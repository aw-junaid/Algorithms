# The Bellman-Ford Algorithm: Detailed Explanation and Implementation

## Introduction to Bellman-Ford Algorithm

The Bellman-Ford algorithm is a graph search algorithm that computes shortest paths from a single source vertex to all other vertices in a weighted digraph. Unlike Dijkstra's algorithm, which only works with graphs with non-negative weights, Bellman-Ford can handle graphs where some edge weights are negative.

## Key Characteristics

1. **Single-source shortest paths**: Finds shortest paths from one source node to all other nodes
2. **Handles negative weights**: Can detect negative weight cycles
3. **Time complexity**: O(V*E) where V is number of vertices and E is number of edges
4. **Space complexity**: O(V)

## How Bellman-Ford Works

The algorithm works by relaxation, where the shortest distance to all vertices is gradually replaced by more accurate values until they reach the optimum solution.

### Main Steps:

1. Initialize distances from the source to all vertices as infinity, except the source itself which is zero.
2. Relax all edges |V| - 1 times (where |V| is number of vertices)
3. Check for negative weight cycles by attempting to relax all edges one more time
4. If any distance can still be improved, a negative cycle exists

### Edge Relaxation

For each edge (u, v) with weight w:
```
if (distance[u] + w < distance[v])
    distance[v] = distance[u] + w
```

## Applications

- Routing protocols in computer networks
- Currency exchange rate calculations
- Traffic engineering
- Any scenario requiring shortest paths with possible negative weights

## Advantages

- Can handle negative weight edges
- Can detect negative weight cycles
- Simpler than Dijkstra's for distributed systems

## Disadvantages

- Slower than Dijkstra's for graphs without negative edges
- Not suitable for graphs with negative cycles reachable from source

## C Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

// Structure to represent a weighted edge in graph
struct Edge {
    int src, dest, weight;
};

// Structure to represent a connected, directed and weighted graph
struct Graph {
    int V, E;
    struct Edge* edge;
};

// Creates a graph with V vertices and E edges
struct Graph* createGraph(int V, int E) {
    struct Graph* graph = (struct Graph*)malloc(sizeof(struct Graph));
    graph->V = V;
    graph->E = E;
    graph->edge = (struct Edge*)malloc(E * sizeof(struct Edge));
    return graph;
}

// The main function that finds shortest distances from src to all other
// vertices using Bellman-Ford algorithm. The function also detects negative
// weight cycle
void BellmanFord(struct Graph* graph, int src) {
    int V = graph->V;
    int E = graph->E;
    int dist[V];
    
    // Step 1: Initialize distances from src to all other vertices as INFINITE
    for (int i = 0; i < V; i++)
        dist[i] = INT_MAX;
    dist[src] = 0;
    
    // Step 2: Relax all edges |V| - 1 times
    for (int i = 1; i <= V - 1; i++) {
        for (int j = 0; j < E; j++) {
            int u = graph->edge[j].src;
            int v = graph->edge[j].dest;
            int weight = graph->edge[j].weight;
            if (dist[u] != INT_MAX && dist[u] + weight < dist[v])
                dist[v] = dist[u] + weight;
        }
    }
    
    // Step 3: Check for negative-weight cycles
    for (int i = 0; i < E; i++) {
        int u = graph->edge[i].src;
        int v = graph->edge[i].dest;
        int weight = graph->edge[i].weight;
        if (dist[u] != INT_MAX && dist[u] + weight < dist[v]) {
            printf("Graph contains negative weight cycle\n");
            return;
        }
    }
    
    // Step 4: Print the distances
    printf("Vertex   Distance from Source\n");
    for (int i = 0; i < V; ++i)
        printf("%d \t\t %d\n", i, dist[i]);
    
    return;
}

int main() {
    int V = 5; // Number of vertices in graph
    int E = 8; // Number of edges in graph
    struct Graph* graph = createGraph(V, E);
    
    // Add edge 0-1 (or A-B in example)
    graph->edge[0].src = 0;
    graph->edge[0].dest = 1;
    graph->edge[0].weight = -1;
    
    // Add edge 0-2 (or A-C in example)
    graph->edge[1].src = 0;
    graph->edge[1].dest = 2;
    graph->edge[1].weight = 4;
    
    // Add edge 1-2 (or B-C in example)
    graph->edge[2].src = 1;
    graph->edge[2].dest = 2;
    graph->edge[2].weight = 3;
    
    // Add edge 1-3 (or B-D in example)
    graph->edge[3].src = 1;
    graph->edge[3].dest = 3;
    graph->edge[3].weight = 2;
    
    // Add edge 1-4 (or B-E in example)
    graph->edge[4].src = 1;
    graph->edge[4].dest = 4;
    graph->edge[4].weight = 2;
    
    // Add edge 3-2 (or D-C in example)
    graph->edge[5].src = 3;
    graph->edge[5].dest = 2;
    graph->edge[5].weight = 5;
    
    // Add edge 3-1 (or D-B in example)
    graph->edge[6].src = 3;
    graph->edge[6].dest = 1;
    graph->edge[6].weight = 1;
    
    // Add edge 4-3 (or E-D in example)
    graph->edge[7].src = 4;
    graph->edge[7].dest = 3;
    graph->edge[7].weight = -3;
    
    BellmanFord(graph, 0);
    
    return 0;
}
```

## Explanation of the Implementation

1. **Graph Representation**: The graph is represented using structures for edges and the graph itself.
2. **Initialization**: All distances are set to infinity except the source node which is set to 0.
3. **Relaxation**: All edges are relaxed V-1 times to find the shortest paths.
4. **Negative Cycle Check**: After V-1 relaxations, if we can still relax any edge, a negative cycle exists.
5. **Output**: Finally, the distances from the source to all other nodes are printed.

## Example Output

For the given example graph, the output would be:
```
Vertex   Distance from Source
0                0
1                -1
2                2
3                -2
4                1
```

This indicates the shortest path distances from vertex 0 to all other vertices in the graph.
