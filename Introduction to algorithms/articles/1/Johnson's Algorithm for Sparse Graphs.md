# Johnson's Algorithm for Sparse Graphs

Johnson's algorithm is a way to find the shortest paths between all pairs of vertices in a weighted, directed graph. It's particularly efficient for sparse graphs (graphs with relatively few edges compared to vertices) because it combines the Bellman-Ford algorithm with Dijkstra's algorithm to achieve better performance than Floyd-Warshall for such cases.

## Key Features

1. **Handles negative weights**: Unlike Dijkstra's algorithm alone, Johnson's can handle graphs with negative edge weights (as long as there are no negative cycles)
2. **All-pairs shortest paths**: Finds shortest paths between every pair of vertices
3. **Good for sparse graphs**: Performs better than Floyd-Warshall when the graph is sparse (|E| << |V|²)

## Algorithm Steps

1. **Add a new vertex**: Add a new vertex q to the graph, connected with zero-weight edges to all other vertices
2. **Run Bellman-Ford**: Use Bellman-Ford starting from q to compute the minimum distance h(v) from q to each vertex v
3. **Check for negative cycles**: If Bellman-Ford detects a negative cycle, report it and exit
4. **Reweight edges**: For each edge (u, v) with weight w(u, v), compute a new weight w'(u, v) = w(u, v) + h(u) - h(v)
5. **Run Dijkstra for each vertex**: For each vertex u, run Dijkstra's algorithm using the reweighted edges to compute the shortest paths from u
6. **Compute original distances**: For each shortest path distance D'(u, v), compute the original distance D(u, v) = D'(u, v) - h(u) + h(v)

## Why It Works

The reweighting step ensures all edge weights become non-negative (which allows Dijkstra's to work) while preserving the relative shortest paths. The final adjustment converts the distances back to the original weight scale.

## Time Complexity

- O(V E) for Bellman-Ford (step 2)
- O(V E log V) for V runs of Dijkstra's (step 5)
- Total: O(V E log V) which is better than Floyd-Warshall's O(V³) for sparse graphs

## C Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>
#include <stdbool.h>

#define MAX_VERTICES 100
#define INF INT_MAX

typedef struct {
    int dest;
    int weight;
} Edge;

typedef struct {
    Edge* edges[MAX_VERTICES];
    int edgeCount[MAX_VERTICES];
    int vertexCount;
} Graph;

Graph* createGraph(int vertexCount) {
    Graph* graph = (Graph*)malloc(sizeof(Graph));
    graph->vertexCount = vertexCount;
    for (int i = 0; i < vertexCount; ++i) {
        graph->edges[i] = (Edge*)malloc(MAX_VERTICES * sizeof(Edge));
        graph->edgeCount[i] = 0;
    }
    return graph;
}

void addEdge(Graph* graph, int src, int dest, int weight) {
    graph->edges[src][graph->edgeCount[src]].dest = dest;
    graph->edges[src][graph->edgeCount[src]].weight = weight;
    graph->edgeCount[src]++;
}

bool bellmanFord(Graph* graph, int src, int dist[]) {
    int V = graph->vertexCount;
    for (int i = 0; i < V; ++i)
        dist[i] = INF;
    dist[src] = 0;

    // Relax all edges V-1 times
    for (int i = 1; i <= V - 1; ++i) {
        for (int u = 0; u < V; ++u) {
            for (int j = 0; j < graph->edgeCount[u]; ++j) {
                int v = graph->edges[u][j].dest;
                int weight = graph->edges[u][j].weight;
                if (dist[u] != INF && dist[u] + weight < dist[v])
                    dist[v] = dist[u] + weight;
            }
        }
    }

    // Check for negative cycles
    for (int u = 0; u < V; ++u) {
        for (int j = 0; j < graph->edgeCount[u]; ++j) {
            int v = graph->edges[u][j].dest;
            int weight = graph->edges[u][j].weight;
            if (dist[u] != INF && dist[u] + weight < dist[v])
                return false; // Negative cycle found
        }
    }
    return true;
}

int minDistance(int dist[], bool sptSet[], int V) {
    int min = INF, min_index;
    for (int v = 0; v < V; v++)
        if (!sptSet[v] && dist[v] <= min)
            min = dist[v], min_index = v;
    return min_index;
}

void dijkstra(Graph* graph, int src, int dist[], int h[]) {
    int V = graph->vertexCount;
    bool sptSet[V];
    
    for (int i = 0; i < V; i++) {
        dist[i] = INF;
        sptSet[i] = false;
    }
    
    dist[src] = 0;

    for (int count = 0; count < V - 1; count++) {
        int u = minDistance(dist, sptSet, V);
        sptSet[u] = true;

        for (int j = 0; j < graph->edgeCount[u]; j++) {
            int v = graph->edges[u][j].dest;
            int weight = graph->edges[u][j].weight + h[u] - h[v]; // Reweighted
            
            if (!sptSet[v] && dist[u] != INF && dist[u] + weight < dist[v])
                dist[v] = dist[u] + weight;
        }
    }
}

void johnson(Graph* graph) {
    int V = graph->vertexCount;
    
    // Add a new vertex connected to all others with 0 weight
    int newVertex = V;
    graph->vertexCount++;
    graph->edgeCount[newVertex] = V;
    for (int i = 0; i < V; i++) {
        graph->edges[newVertex][i].dest = i;
        graph->edges[newVertex][i].weight = 0;
    }

    // Step 1: Run Bellman-Ford from new vertex
    int h[V+1];
    if (!bellmanFord(graph, newVertex, h)) {
        printf("Graph contains negative weight cycle\n");
        return;
    }
    
    // Remove the added vertex
    graph->vertexCount--;
    
    // Step 2: Reweight all edges
    for (int u = 0; u < V; u++) {
        for (int j = 0; j < graph->edgeCount[u]; j++) {
            int v = graph->edges[u][j].dest;
            graph->edges[u][j].weight += h[u] - h[v];
        }
    }
    
    // Step 3: Run Dijkstra for each vertex
    int dist[V];
    int allPairs[V][V];
    
    printf("All pairs shortest paths:\n");
    for (int u = 0; u < V; u++) {
        dijkstra(graph, u, dist, h);
        
        // Store and print results
        for (int v = 0; v < V; v++) {
            if (dist[v] == INF)
                allPairs[u][v] = INF;
            else
                allPairs[u][v] = dist[v] - h[u] + h[v];
                
            if (allPairs[u][v] == INF)
                printf("%7s", "INF");
            else
                printf("%7d", allPairs[u][v]);
        }
        printf("\n");
    }
}

int main() {
    int V = 4;
    Graph* graph = createGraph(V);
    
    addEdge(graph, 0, 1, -5);
    addEdge(graph, 0, 2, 2);
    addEdge(graph, 0, 3, 3);
    addEdge(graph, 1, 2, 4);
    addEdge(graph, 2, 3, 1);
    
    johnson(graph);
    
    return 0;
}
```

## Explanation of the Implementation

1. **Graph Representation**: Uses an adjacency list to store the graph (more efficient for sparse graphs)
2. **Bellman-Ford**: Used to compute the initial potential values (h) and detect negative cycles
3. **Dijkstra's Algorithm**: Used with the reweighted edges to compute shortest paths from each vertex
4. **Reweighting**: Adjusts edge weights to be non-negative while preserving shortest paths
5. **Result Adjustment**: Converts the distances back to the original weight scale

## When to Use Johnson's Algorithm

- When you need all-pairs shortest paths in a sparse graph
- When the graph may contain negative weights (but no negative cycles)
- When the graph is large enough that Floyd-Warshall's O(V³) would be too slow

For dense graphs, Floyd-Warshall might be more appropriate despite its higher time complexity, as it has lower constant factors.
