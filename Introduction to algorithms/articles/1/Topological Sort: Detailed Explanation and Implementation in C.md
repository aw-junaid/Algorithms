# Topological Sort: Detailed Explanation and Implementation in C

## What is Topological Sort?

Topological sort is a linear ordering of the vertices of a directed acyclic graph (DAG) such that for every directed edge (u, v), vertex u comes before vertex v in the ordering. It's important to note that topological sort only exists for DAGs - graphs with no directed cycles.

## Key Concepts

1. **Directed Acyclic Graph (DAG)**: A graph with directed edges and no cycles.
2. **Dependencies**: If there's an edge from A to B, A must come before B in the ordering.
3. **Partial Order**: Multiple valid topological orders may exist for the same graph.

## Applications of Topological Sort

- Task scheduling
- Build systems (determining compilation order)
- Course prerequisites
- Event scheduling
- Dependency resolution

## Algorithms for Topological Sort

There are two main approaches:

### 1. Kahn's Algorithm (BFS-based)
### 2. DFS-based Algorithm

## Kahn's Algorithm (BFS-based)

**Steps:**
1. Compute in-degree (number of incoming edges) for each node
2. Enqueue all nodes with in-degree 0
3. While queue is not empty:
   - Dequeue a node and add it to the topological order
   - Reduce in-degree of all its neighbors by 1
   - If a neighbor's in-degree becomes 0, enqueue it
4. If topological order contains all nodes, return it; else graph has a cycle

### Implementation in C (Kahn's Algorithm)

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX_NODES 100

typedef struct Node {
    int vertex;
    struct Node* next;
} Node;

typedef struct Graph {
    int numVertices;
    Node** adjLists;
    int* inDegree;
} Graph;

Node* createNode(int v) {
    Node* newNode = malloc(sizeof(Node));
    newNode->vertex = v;
    newNode->next = NULL;
    return newNode;
}

Graph* createGraph(int vertices) {
    Graph* graph = malloc(sizeof(Graph));
    graph->numVertices = vertices;
    graph->adjLists = malloc(vertices * sizeof(Node*));
    graph->inDegree = malloc(vertices * sizeof(int));
    
    for (int i = 0; i < vertices; i++) {
        graph->adjLists[i] = NULL;
        graph->inDegree[i] = 0;
    }
    
    return graph;
}

void addEdge(Graph* graph, int src, int dest) {
    Node* newNode = createNode(dest);
    newNode->next = graph->adjLists[src];
    graph->adjLists[src] = newNode;
    graph->inDegree[dest]++;
}

void topologicalSort(Graph* graph) {
    int queue[MAX_NODES];
    int front = 0, rear = 0;
    
    // Enqueue all vertices with in-degree 0
    for (int i = 0; i < graph->numVertices; i++) {
        if (graph->inDegree[i] == 0) {
            queue[rear++] = i;
        }
    }
    
    int count = 0; // Count of visited vertices
    int topOrder[MAX_NODES]; // Topological order
    
    while (front != rear) {
        int u = queue[front++];
        topOrder[count++] = u;
        
        // Reduce in-degree of all adjacent vertices
        Node* temp = graph->adjLists[u];
        while (temp != NULL) {
            int v = temp->vertex;
            if (--graph->inDegree[v] == 0) {
                queue[rear++] = v;
            }
            temp = temp->next;
        }
    }
    
    // Check for cycle
    if (count != graph->numVertices) {
        printf("Graph has a cycle!\n");
        return;
    }
    
    // Print topological order
    printf("Topological Sort: ");
    for (int i = 0; i < count; i++) {
        printf("%d ", topOrder[i]);
    }
    printf("\n");
}

int main() {
    Graph* graph = createGraph(6);
    
    addEdge(graph, 5, 2);
    addEdge(graph, 5, 0);
    addEdge(graph, 4, 0);
    addEdge(graph, 4, 1);
    addEdge(graph, 2, 3);
    addEdge(graph, 3, 1);
    
    topologicalSort(graph);
    
    return 0;
}
```

## DFS-based Algorithm

**Steps:**
1. Perform DFS on the graph
2. After visiting all neighbors of a node, push it onto a stack
3. The stack will contain the topological order when DFS completes

### Implementation in C (DFS-based)

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

#define MAX_NODES 100

typedef struct Node {
    int vertex;
    struct Node* next;
} Node;

typedef struct Graph {
    int numVertices;
    Node** adjLists;
    bool* visited;
} Graph;

Node* createNode(int v) {
    Node* newNode = malloc(sizeof(Node));
    newNode->vertex = v;
    newNode->next = NULL;
    return newNode;
}

Graph* createGraph(int vertices) {
    Graph* graph = malloc(sizeof(Graph));
    graph->numVertices = vertices;
    graph->adjLists = malloc(vertices * sizeof(Node*));
    graph->visited = malloc(vertices * sizeof(bool));
    
    for (int i = 0; i < vertices; i++) {
        graph->adjLists[i] = NULL;
        graph->visited[i] = false;
    }
    
    return graph;
}

void addEdge(Graph* graph, int src, int dest) {
    Node* newNode = createNode(dest);
    newNode->next = graph->adjLists[src];
    graph->adjLists[src] = newNode;
}

void topologicalSortUtil(Graph* graph, int v, int* stack, int* top) {
    graph->visited[v] = true;
    
    Node* temp = graph->adjLists[v];
    while (temp != NULL) {
        int adjVertex = temp->vertex;
        if (!graph->visited[adjVertex]) {
            topologicalSortUtil(graph, adjVertex, stack, top);
        }
        temp = temp->next;
    }
    
    stack[++(*top)] = v;
}

void topologicalSort(Graph* graph) {
    int stack[MAX_NODES];
    int top = -1;
    
    for (int i = 0; i < graph->numVertices; i++) {
        if (!graph->visited[i]) {
            topologicalSortUtil(graph, i, stack, &top);
        }
    }
    
    printf("Topological Sort: ");
    while (top >= 0) {
        printf("%d ", stack[top--]);
    }
    printf("\n");
}

int main() {
    Graph* graph = createGraph(6);
    
    addEdge(graph, 5, 2);
    addEdge(graph, 5, 0);
    addEdge(graph, 4, 0);
    addEdge(graph, 4, 1);
    addEdge(graph, 2, 3);
    addEdge(graph, 3, 1);
    
    topologicalSort(graph);
    
    return 0;
}
```

## Time Complexity

Both algorithms have a time complexity of O(V + E), where V is the number of vertices and E is the number of edges in the graph.

## Space Complexity

The space complexity is O(V) for both algorithms, primarily for storing the in-degree array (Kahn's) or the recursion stack (DFS).

## Handling Cycles

If the graph contains a cycle:
- Kahn's algorithm will detect it when the topological order contains fewer nodes than the total nodes in the graph
- The DFS-based approach would need additional tracking (like recursion stack) to detect cycles

## Choosing Between Algorithms

- **Kahn's algorithm** is often preferred when you need to detect cycles
- **DFS-based approach** might be simpler to implement and uses less memory for large graphs
- Kahn's algorithm processes nodes level by level, which can be useful in some scheduling scenarios

Both implementations provide valid topological sorts, though the order may differ between them for the same graph.
