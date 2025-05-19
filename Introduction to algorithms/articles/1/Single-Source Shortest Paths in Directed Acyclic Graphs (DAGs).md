# Single-Source Shortest Paths in Directed Acyclic Graphs (DAGs)

## Introduction

The single-source shortest paths (SSSP) problem in a directed acyclic graph (DAG) is a special case of the general shortest path problem that can be solved more efficiently due to the lack of cycles in the graph. This allows us to process the vertices in a linear order.

## Key Concepts

### 1. Directed Acyclic Graph (DAG)
A directed graph with no directed cycles. This means it's impossible to start at any vertex v and follow a sequence of edges that eventually loops back to v again.

### 2. Topological Sorting
A linear ordering of the vertices such that for every directed edge (u, v), vertex u comes before v in the ordering. This is possible if and only if the graph is a DAG.

### 3. Relaxation
The process of improving the current shortest path estimate for a vertex by considering paths through its neighbors.

## Algorithm Overview

The algorithm for finding shortest paths in a DAG works as follows:

1. Topologically sort the vertices of the DAG
2. Initialize distances from the source to all vertices as infinity, except the source itself which is 0
3. Process vertices in topological order
   - For each vertex, relax all its outgoing edges

## Why It Works

The topological sort ensures that when we process a vertex, we've already processed all vertices that can reach it. This means the shortest path to the current vertex is already known before we use it to relax its neighbors.

## Time Complexity

- Topological sort: O(V + E)
- Relaxation process: O(V + E)
- Total: O(V + E) - much more efficient than Dijkstra's O((V+E)logV) or Bellman-Ford's O(VE)

## Implementation in C

```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

#define MAX_VERTICES 100

// Structure to represent a weighted edge in graph
struct Edge {
    int dest;
    int weight;
    struct Edge* next;
};

// Structure to represent a graph
struct Graph {
    struct Edge* adj[MAX_VERTICES]; // Adjacency list
    int V; // Number of vertices
};

// Function to create a new graph
struct Graph* createGraph(int V) {
    struct Graph* graph = (struct Graph*)malloc(sizeof(struct Graph));
    graph->V = V;
    for (int i = 0; i < V; ++i)
        graph->adj[i] = NULL;
    return graph;
}

// Function to add an edge to the graph
void addEdge(struct Graph* graph, int src, int dest, int weight) {
    struct Edge* newEdge = (struct Edge*)malloc(sizeof(struct Edge));
    newEdge->dest = dest;
    newEdge->weight = weight;
    newEdge->next = graph->adj[src];
    graph->adj[src] = newEdge;
}

// A recursive function used by topologicalSort
void topologicalSortUtil(struct Graph* graph, int v, int visited[], int* stack, int* stackIndex) {
    visited[v] = 1;
    
    // Recur for all adjacent vertices
    struct Edge* edge = graph->adj[v];
    while (edge != NULL) {
        if (!visited[edge->dest])
            topologicalSortUtil(graph, edge->dest, visited, stack, stackIndex);
        edge = edge->next;
    }
    
    // Push current vertex to stack
    stack[(*stackIndex)++] = v;
}

// Function to perform topological sort
void topologicalSort(struct Graph* graph, int* order) {
    int visited[MAX_VERTICES] = {0};
    int stackIndex = 0;
    
    for (int i = 0; i < graph->V; i++)
        if (!visited[i])
            topologicalSortUtil(graph, i, visited, order, &stackIndex);
    
    // The topological order is in reverse order of stack
    // So we need to reverse it
    for (int i = 0; i < graph->V / 2; i++) {
        int temp = order[i];
        order[i] = order[graph->V - 1 - i];
        order[graph->V - 1 - i] = temp;
    }
}

// Function to find shortest paths from a given source
void shortestPath(struct Graph* graph, int src) {
    int V = graph->V;
    int dist[V];
    int order[V]; // To store topological order
    
    // Initialize distances to all vertices as infinite
    for (int i = 0; i < V; i++)
        dist[i] = INT_MAX;
    dist[src] = 0;
    
    // Get topological order
    topologicalSort(graph, order);
    
    // Process vertices in topological order
    for (int i = 0; i < V; i++) {
        int u = order[i];
        
        // Update distances of all adjacent vertices
        if (dist[u] != INT_MAX) {
            struct Edge* edge = graph->adj[u];
            while (edge != NULL) {
                int v = edge->dest;
                if (dist[v] > dist[u] + edge->weight)
                    dist[v] = dist[u] + edge->weight;
                edge = edge->next;
            }
        }
    }
    
    // Print the calculated shortest distances
    printf("Vertex\tDistance from Source\n");
    for (int i = 0; i < V; i++) {
        if (dist[i] == INT_MAX)
            printf("%d\tINF\n", i);
        else
            printf("%d\t%d\n", i, dist[i]);
    }
}

// Driver program to test above functions
int main() {
    int V = 6;
    struct Graph* graph = createGraph(V);
    
    addEdge(graph, 0, 1, 5);
    addEdge(graph, 0, 2, 3);
    addEdge(graph, 1, 3, 6);
    addEdge(graph, 1, 2, 2);
    addEdge(graph, 2, 4, 4);
    addEdge(graph, 2, 5, 2);
    addEdge(graph, 2, 3, 7);
    addEdge(graph, 3, 4, -1);
    addEdge(graph, 4, 5, -2);
    
    int source = 1;
    shortestPath(graph, source);
    
    return 0;
}
```

## Explanation of the Implementation

1. **Graph Representation**: The graph is represented using adjacency lists where each vertex maintains a linked list of its outgoing edges.

2. **Topological Sort**: 
   - Performed using DFS
   - Vertices are pushed to a stack in post-order
   - The stack is then reversed to get the topological order

3. **Shortest Path Calculation**:
   - Initialize all distances to infinity except the source
   - Process vertices in topological order
   - For each vertex, relax all its outgoing edges
   - If a shorter path is found through a neighbor, update the distance

4. **Output**: Prints the shortest distance from the source to all other vertices

## Advantages of This Approach

1. **Efficiency**: O(V+E) time complexity is better than general shortest path algorithms
2. **Handles Negative Weights**: Unlike Dijkstra's algorithm, this works with negative edge weights
3. **Deterministic**: Doesn't rely on priority queues or other complex data structures

## Limitations

1. **Only for DAGs**: Cannot be used for graphs with cycles
2. **Single Source**: Needs to be rerun for different sources

This algorithm is particularly useful in scheduling problems, critical path analysis, and other applications where the input is naturally a DAG.
