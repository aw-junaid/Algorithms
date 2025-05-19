# Strongly Connected Components (SCC) in Graph Theory

## Introduction to Strongly Connected Components

A strongly connected component (SCC) of a directed graph is a maximal subgraph where every pair of vertices is mutually reachable. In other words, for any two vertices u and v in an SCC, there exists a path from u to v and a path from v to u.

## Key Concepts

1. **Directed Graphs**: SCCs are only meaningful for directed graphs since in undirected graphs we have connected components.

2. **Maximal**: We cannot add any more vertices to an SCC without breaking the strongly connected property.

3. **Mutual Reachability**: All nodes in an SCC can reach each other.

## Kosaraju's Algorithm for Finding SCCs

One of the most efficient algorithms for finding SCCs is Kosaraju's algorithm, which runs in O(V+E) time (linear time).

### Algorithm Steps:

1. **First Pass (Compute Finishing Times)**:
   - Perform a DFS on the original graph, pushing vertices onto a stack in order of their finishing times.

2. **Transpose the Graph**:
   - Create the transpose of the original graph (reverse all edges).

3. **Second Pass (Process in Reverse Order)**:
   - Perform DFS on the transposed graph in the order defined by the stack (from highest finishing time to lowest).
   - Each DFS tree in this pass forms an SCC.

### Why Kosaraju's Algorithm Works

- The first DFS identifies vertices in order of their finishing times.
- Processing the transposed graph in reverse finishing time order ensures we start from sink components (SCCs with no outgoing edges to other SCCs).
- The transposed graph has exactly the same SCCs as the original graph.

## Implementation in C

Here's a complete implementation of Kosaraju's algorithm in C:

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

#define MAX_VERTICES 100

typedef struct Node {
    int vertex;
    struct Node* next;
} Node;

typedef struct Graph {
    int numVertices;
    Node** adjLists;
} Graph;

// Stack structure for DFS
typedef struct Stack {
    int data[MAX_VERTICES];
    int top;
} Stack;

// Function prototypes
Graph* createGraph(int vertices);
void addEdge(Graph* graph, int src, int dest);
Graph* transposeGraph(Graph* graph);
void fillOrder(Graph* graph, int v, bool visited[], Stack* stack);
void DFSUtil(Graph* graph, int v, bool visited[]);
void printSCCs(Graph* graph);
void push(Stack* stack, int item);
int pop(Stack* stack);
bool isEmpty(Stack* stack);

// Create a graph with given number of vertices
Graph* createGraph(int vertices) {
    Graph* graph = malloc(sizeof(Graph));
    graph->numVertices = vertices;
    graph->adjLists = malloc(vertices * sizeof(Node*));
    
    for (int i = 0; i < vertices; i++) {
        graph->adjLists[i] = NULL;
    }
    
    return graph;
}

// Add an edge from src to dest
void addEdge(Graph* graph, int src, int dest) {
    Node* newNode = malloc(sizeof(Node));
    newNode->vertex = dest;
    newNode->next = graph->adjLists[src];
    graph->adjLists[src] = newNode;
}

// Transpose the graph (reverse all edges)
Graph* transposeGraph(Graph* graph) {
    Graph* transposed = createGraph(graph->numVertices);
    
    for (int v = 0; v < graph->numVertices; v++) {
        Node* temp = graph->adjLists[v];
        while (temp) {
            addEdge(transposed, temp->vertex, v);
            temp = temp->next;
        }
    }
    
    return transposed;
}

// Push an item onto the stack
void push(Stack* stack, int item) {
    stack->data[++stack->top] = item;
}

// Pop an item from the stack
int pop(Stack* stack) {
    return stack->data[stack->top--];
}

// Check if stack is empty
bool isEmpty(Stack* stack) {
    return stack->top == -1;
}

// Fill the stack with vertices in order of finishing times
void fillOrder(Graph* graph, int v, bool visited[], Stack* stack) {
    visited[v] = true;
    
    Node* temp = graph->adjLists[v];
    while (temp) {
        if (!visited[temp->vertex]) {
            fillOrder(graph, temp->vertex, visited, stack);
        }
        temp = temp->next;
    }
    
    push(stack, v);
}

// DFS traversal of the graph
void DFSUtil(Graph* graph, int v, bool visited[]) {
    visited[v] = true;
    printf("%d ", v);
    
    Node* temp = graph->adjLists[v];
    while (temp) {
        if (!visited[temp->vertex]) {
            DFSUtil(graph, temp->vertex, visited);
        }
        temp = temp->next;
    }
}

// The main function that finds and prints all strongly connected components
void printSCCs(Graph* graph) {
    Stack stack = { .top = -1 };
    bool* visited = malloc(graph->numVertices * sizeof(bool));
    
    // Initialize visited as false
    for (int i = 0; i < graph->numVertices; i++) {
        visited[i] = false;
    }
    
    // Fill vertices in stack according to their finishing times
    for (int i = 0; i < graph->numVertices; i++) {
        if (!visited[i]) {
            fillOrder(graph, i, visited, &stack);
        }
    }
    
    // Create a reversed graph
    Graph* transposed = transposeGraph(graph);
    
    // Mark all vertices as not visited for second DFS
    for (int i = 0; i < graph->numVertices; i++) {
        visited[i] = false;
    }
    
    // Process all vertices in order defined by stack
    printf("Strongly Connected Components:\n");
    while (!isEmpty(&stack)) {
        int v = pop(&stack);
        
        if (!visited[v]) {
            DFSUtil(transposed, v, visited);
            printf("\n");
        }
    }
    
    free(visited);
    free(transposed->adjLists);
    free(transposed);
}

int main() {
    // Create a graph with 5 vertices
    Graph* graph = createGraph(5);
    
    // Add edges
    addEdge(graph, 1, 0);
    addEdge(graph, 0, 2);
    addEdge(graph, 2, 1);
    addEdge(graph, 0, 3);
    addEdge(graph, 3, 4);
    
    printf("Following are strongly connected components in the given graph:\n");
    printSCCs(graph);
    
    return 0;
}
```

## Explanation of the Implementation

1. **Graph Representation**: The graph is represented using adjacency lists.

2. **Stack Operations**: A stack is used to store vertices in order of their finishing times during the first DFS pass.

3. **Transpose Graph**: The algorithm creates a transposed version of the original graph where all edges are reversed.

4. **Two DFS Passes**:
   - First DFS fills the stack with vertices ordered by finishing times.
   - Second DFS processes the transposed graph in the order defined by the stack, printing each SCC.

5. **Time Complexity**: O(V+E) where V is the number of vertices and E is the number of edges.

## Applications of Strongly Connected Components

1. **Simplifying Complex Graphs**: Breaking down large graphs into SCCs can simplify analysis.
2. **Compiler Optimization**: Used in control flow analysis.
3. **Social Networks**: Identifying tightly-knit communities.
4. **Web Page Ranking**: Used in algorithms like PageRank.
5. **Circuit Design**: For analyzing electronic circuits.

This implementation provides a complete solution for finding strongly connected components in a directed graph using Kosaraju's algorithm in C.
