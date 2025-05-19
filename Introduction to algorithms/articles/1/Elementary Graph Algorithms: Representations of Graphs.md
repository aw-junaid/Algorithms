# Elementary Graph Algorithms: Representations of Graphs

Graphs are fundamental data structures in computer science used to model relationships between objects. Understanding graph representations is crucial before implementing graph algorithms. Let's explore this topic in detail.

## Graph Fundamentals

A graph G consists of:
- A set of vertices (or nodes) V
- A set of edges E connecting pairs of vertices

Graphs can be:
- **Directed (digraph)**: Edges have direction (u → v)
- **Undirected**: Edges have no direction (u-v)

## Common Graph Representations

### 1. Adjacency Matrix

A 2D array where:
- Rows and columns represent vertices
- Matrix[i][j] indicates presence/weight of edge between vertex i and j

**Pros:**
- Simple to implement
- O(1) edge lookup
- Easy to check for edge existence

**Cons:**
- O(V²) space complexity (inefficient for sparse graphs)
- Adding/removing vertices is expensive

### 2. Adjacency List

An array of linked lists where:
- Each array element represents a vertex
- Each linked list contains neighbors of that vertex

**Pros:**
- Space efficient (O(V + E))
- Easy to iterate through neighbors
- Good for sparse graphs

**Cons:**
- Edge lookup is O(degree(V)) instead of O(1)
- Slightly more complex implementation

### 3. Edge List

A simple list of all edges, often with their weights.

**Pros:**
- Simple representation
- Memory efficient for some algorithms

**Cons:**
- Inefficient for most operations

## C Implementation of Graph Representations

Here's a complete C implementation demonstrating all three representations:

```c
#include <stdio.h>
#include <stdlib.h>

// Node for adjacency list
typedef struct Node {
    int vertex;
    struct Node* next;
} Node;

// Graph structure that includes all representations
typedef struct Graph {
    int numVertices;
    
    // Adjacency matrix
    int** adjMatrix;
    
    // Adjacency list
    Node** adjList;
    
    // Edge list (for demonstration, using fixed size)
    struct {
        int u;
        int v;
        int weight; // For weighted graphs
    } edgeList[100];
    int edgeCount;
} Graph;

// Create a new node for adjacency list
Node* createNode(int v) {
    Node* newNode = malloc(sizeof(Node));
    newNode->vertex = v;
    newNode->next = NULL;
    return newNode;
}

// Initialize a graph with given number of vertices
Graph* createGraph(int vertices) {
    Graph* graph = malloc(sizeof(Graph));
    graph->numVertices = vertices;
    graph->edgeCount = 0;
    
    // Initialize adjacency matrix
    graph->adjMatrix = malloc(vertices * sizeof(int*));
    for (int i = 0; i < vertices; i++) {
        graph->adjMatrix[i] = malloc(vertices * sizeof(int));
        for (int j = 0; j < vertices; j++) {
            graph->adjMatrix[i][j] = 0;
        }
    }
    
    // Initialize adjacency list
    graph->adjList = malloc(vertices * sizeof(Node*));
    for (int i = 0; i < vertices; i++) {
        graph->adjList[i] = NULL;
    }
    
    return graph;
}

// Add edge to all representations
void addEdge(Graph* graph, int src, int dest, int isDirected, int weight) {
    // Add to adjacency matrix
    graph->adjMatrix[src][dest] = weight;
    if (!isDirected) {
        graph->adjMatrix[dest][src] = weight;
    }
    
    // Add to adjacency list
    Node* newNode = createNode(dest);
    newNode->next = graph->adjList[src];
    graph->adjList[src] = newNode;
    
    if (!isDirected) {
        newNode = createNode(src);
        newNode->next = graph->adjList[dest];
        graph->adjList[dest] = newNode;
    }
    
    // Add to edge list
    graph->edgeList[graph->edgeCount].u = src;
    graph->edgeList[graph->edgeCount].v = dest;
    graph->edgeList[graph->edgeCount].weight = weight;
    graph->edgeCount++;
}

// Print adjacency matrix
void printAdjMatrix(Graph* graph) {
    printf("Adjacency Matrix:\n");
    for (int i = 0; i < graph->numVertices; i++) {
        for (int j = 0; j < graph->numVertices; j++) {
            printf("%d ", graph->adjMatrix[i][j]);
        }
        printf("\n");
    }
}

// Print adjacency list
void printAdjList(Graph* graph) {
    printf("Adjacency List:\n");
    for (int i = 0; i < graph->numVertices; i++) {
        Node* temp = graph->adjList[i];
        printf("Vertex %d: ", i);
        while (temp) {
            printf("%d -> ", temp->vertex);
            temp = temp->next;
        }
        printf("NULL\n");
    }
}

// Print edge list
void printEdgeList(Graph* graph) {
    printf("Edge List:\n");
    for (int i = 0; i < graph->edgeCount; i++) {
        printf("%d - %d (weight: %d)\n", 
               graph->edgeList[i].u, 
               graph->edgeList[i].v, 
               graph->edgeList[i].weight);
    }
}

// Free memory
void freeGraph(Graph* graph) {
    // Free adjacency matrix
    for (int i = 0; i < graph->numVertices; i++) {
        free(graph->adjMatrix[i]);
    }
    free(graph->adjMatrix);
    
    // Free adjacency list
    for (int i = 0; i < graph->numVertices; i++) {
        Node* temp = graph->adjList[i];
        while (temp) {
            Node* toFree = temp;
            temp = temp->next;
            free(toFree);
        }
    }
    free(graph->adjList);
    
    // Free graph
    free(graph);
}

int main() {
    // Create a graph with 5 vertices
    Graph* graph = createGraph(5);
    
    // Add edges (undirected graph)
    addEdge(graph, 0, 1, 0, 2);
    addEdge(graph, 0, 4, 0, 3);
    addEdge(graph, 1, 2, 0, 5);
    addEdge(graph, 1, 3, 0, 1);
    addEdge(graph, 1, 4, 0, 4);
    addEdge(graph, 2, 3, 0, 6);
    addEdge(graph, 3, 4, 0, 2);
    
    // Print all representations
    printAdjMatrix(graph);
    printf("\n");
    printAdjList(graph);
    printf("\n");
    printEdgeList(graph);
    
    // Clean up
    freeGraph(graph);
    
    return 0;
}
```

## Graph Traversal Algorithms

With graph representations understood, let's implement two fundamental traversal algorithms:

### 1. Breadth-First Search (BFS)

```c
#include <stdbool.h>
#include <limits.h>

// BFS implementation using adjacency list
void BFS(Graph* graph, int startVertex) {
    bool* visited = malloc(graph->numVertices * sizeof(bool));
    for (int i = 0; i < graph->numVertices; i++) {
        visited[i] = false;
    }
    
    // Create a queue for BFS
    int* queue = malloc(graph->numVertices * sizeof(int));
    int front = 0, rear = 0;
    
    // Mark the current node as visited and enqueue it
    visited[startVertex] = true;
    queue[rear++] = startVertex;
    
    printf("BFS starting from vertex %d: ", startVertex);
    
    while (front < rear) {
        // Dequeue a vertex and print it
        int currentVertex = queue[front++];
        printf("%d ", currentVertex);
        
        // Get all adjacent vertices
        Node* temp = graph->adjList[currentVertex];
        while (temp) {
            int adjVertex = temp->vertex;
            if (!visited[adjVertex]) {
                visited[adjVertex] = true;
                queue[rear++] = adjVertex;
            }
            temp = temp->next;
        }
    }
    
    printf("\n");
    free(visited);
    free(queue);
}
```

### 2. Depth-First Search (DFS)

```c
// DFS helper function (recursive)
void DFSUtil(Graph* graph, int vertex, bool* visited) {
    // Mark the current node as visited and print it
    visited[vertex] = true;
    printf("%d ", vertex);
    
    // Recur for all adjacent vertices
    Node* temp = graph->adjList[vertex];
    while (temp) {
        int adjVertex = temp->vertex;
        if (!visited[adjVertex]) {
            DFSUtil(graph, adjVertex, visited);
        }
        temp = temp->next;
    }
}

// DFS implementation using adjacency list
void DFS(Graph* graph, int startVertex) {
    bool* visited = malloc(graph->numVertices * sizeof(bool));
    for (int i = 0; i < graph->numVertices; i++) {
        visited[i] = false;
    }
    
    printf("DFS starting from vertex %d: ", startVertex);
    DFSUtil(graph, startVertex, visited);
    printf("\n");
    
    free(visited);
}
```

## Weighted Graph Algorithms

### Dijkstra's Shortest Path Algorithm

```c
// Utility function to find vertex with minimum distance value
int minDistance(int* dist, bool* sptSet, int vertices) {
    int min = INT_MAX, min_index;
    
    for (int v = 0; v < vertices; v++) {
        if (!sptSet[v] && dist[v] <= min) {
            min = dist[v];
            min_index = v;
        }
    }
    
    return min_index;
}

// Dijkstra's algorithm using adjacency matrix
void dijkstra(Graph* graph, int src) {
    int vertices = graph->numVertices;
    int* dist = malloc(vertices * sizeof(int));
    bool* sptSet = malloc(vertices * sizeof(bool));
    
    for (int i = 0; i < vertices; i++) {
        dist[i] = INT_MAX;
        sptSet[i] = false;
    }
    
    dist[src] = 0;
    
    for (int count = 0; count < vertices - 1; count++) {
        int u = minDistance(dist, sptSet, vertices);
        sptSet[u] = true;
        
        for (int v = 0; v < vertices; v++) {
            if (!sptSet[v] && graph->adjMatrix[u][v] && 
                dist[u] != INT_MAX && 
                dist[u] + graph->adjMatrix[u][v] < dist[v]) {
                dist[v] = dist[u] + graph->adjMatrix[u][v];
            }
        }
    }
    
    printf("Vertex \t Distance from Source\n");
    for (int i = 0; i < vertices; i++) {
        printf("%d \t\t %d\n", i, dist[i]);
    }
    
    free(dist);
    free(sptSet);
}
```

## Complete Example with All Algorithms

Here's how to use all these functions in a complete program:

```c
int main() {
    // Create a graph with 5 vertices
    Graph* graph = createGraph(5);
    
    // Add edges (directed weighted graph this time)
    addEdge(graph, 0, 1, 1, 4);  // 0 -> 1 with weight 4
    addEdge(graph, 0, 2, 1, 1);  // 0 -> 2 with weight 1
    addEdge(graph, 1, 3, 1, 2);  // 1 -> 3 with weight 2
    addEdge(graph, 2, 1, 1, 1);  // 2 -> 1 with weight 1
    addEdge(graph, 2, 3, 1, 5);  // 2 -> 3 with weight 5
    addEdge(graph, 3, 4, 1, 3);  // 3 -> 4 with weight 3
    addEdge(graph, 4, 0, 1, 2);  // 4 -> 0 with weight 2
    
    // Print all representations
    printAdjMatrix(graph);
    printf("\n");
    printAdjList(graph);
    printf("\n");
    printEdgeList(graph);
    printf("\n");
    
    // Perform traversals
    BFS(graph, 0);
    DFS(graph, 0);
    printf("\n");
    
    // Perform Dijkstra's algorithm
    printf("Dijkstra's Shortest Path from vertex 0:\n");
    dijkstra(graph, 0);
    
    // Clean up
    freeGraph(graph);
    
    return 0;
}
```

## Key Takeaways

1. **Adjacency Matrix** is best when:
   - The graph is dense
   - You need frequent edge existence checks
   - Space is not a concern

2. **Adjacency List** is best when:
   - The graph is sparse
   - You need to frequently iterate through neighbors
   - Memory efficiency is important

3. **Edge List** is useful for:
   - Certain algorithms like Kruskal's MST
   - When you need to process all edges sequentially
