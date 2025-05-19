# Depth-First Search (DFS) - Comprehensive Explanation and Implementation in C

Depth-First Search (DFS) is a fundamental graph traversal algorithm that explores as far as possible along each branch before backtracking. It's widely used in many applications including path finding, topological sorting, detecting cycles, and solving puzzles.

## How DFS Works

DFS follows these principles:
1. It starts at a selected node (called the root or source node)
2. Explores as far as possible along each branch before backtracking
3. Uses a stack (either explicitly or implicitly via recursion) to keep track of nodes to visit
4. Typically marks nodes as visited to avoid cycles and redundant processing

### DFS Traversal Types:
1. **Pre-order**: Process the node before its children
2. **In-order** (for binary trees): Process left subtree, then node, then right subtree
3. **Post-order**: Process the node after its children

## Algorithm Steps

1. Start by pushing the root node onto the stack
2. While the stack is not empty:
   a. Pop a node from the stack
   b. If the node has not been visited:
      i. Mark it as visited
      ii. Process the node (print it, etc.)
      iii. Push all its adjacent unvisited nodes onto the stack
3. Repeat until the stack is empty

## Time and Space Complexity

- **Time Complexity**: O(V + E) where V is number of vertices and E is number of edges
- **Space Complexity**: O(V) in worst case (for recursion stack or explicit stack)

## Applications of DFS

1. Finding connected components in a graph
2. Topological sorting
3. Finding strongly connected components
4. Solving puzzles with one solution (e.g., mazes)
5. Path finding
6. Detecting cycles in a graph
7. Generating mazes

## Implementation in C

Here's a complete implementation of DFS in C using both recursive and iterative approaches:

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX_VERTICES 100

// Structure to represent a graph node
struct Node {
    int vertex;
    struct Node* next;
};

// Structure to represent a graph
struct Graph {
    int numVertices;
    struct Node** adjLists;
    int* visited;
};

// Create a node
struct Node* createNode(int v) {
    struct Node* newNode = malloc(sizeof(struct Node));
    newNode->vertex = v;
    newNode->next = NULL;
    return newNode;
}

// Create a graph
struct Graph* createGraph(int vertices) {
    struct Graph* graph = malloc(sizeof(struct Graph));
    graph->numVertices = vertices;
    graph->adjLists = malloc(vertices * sizeof(struct Node*));
    graph->visited = malloc(vertices * sizeof(int));
    
    for (int i = 0; i < vertices; i++) {
        graph->adjLists[i] = NULL;
        graph->visited[i] = 0;
    }
    return graph;
}

// Add edge
void addEdge(struct Graph* graph, int src, int dest) {
    // Add edge from src to dest
    struct Node* newNode = createNode(dest);
    newNode->next = graph->adjLists[src];
    graph->adjLists[src] = newNode;
    
    // Add edge from dest to src (for undirected graph)
    newNode = createNode(src);
    newNode->next = graph->adjLists[dest];
    graph->adjLists[dest] = newNode;
}

// Recursive DFS
void DFS_Recursive(struct Graph* graph, int vertex) {
    struct Node* adjList = graph->adjLists[vertex];
    struct Node* temp = adjList;
    
    graph->visited[vertex] = 1;
    printf("Visited %d \n", vertex);
    
    while (temp != NULL) {
        int connectedVertex = temp->vertex;
        
        if (graph->visited[connectedVertex] == 0) {
            DFS_Recursive(graph, connectedVertex);
        }
        temp = temp->next;
    }
}

// Iterative DFS using a stack
void DFS_Iterative(struct Graph* graph, int startVertex) {
    int stack[MAX_VERTICES];
    int top = -1;
    
    // Push the start vertex onto the stack
    stack[++top] = startVertex;
    
    while (top >= 0) {
        // Pop a vertex from stack
        int currentVertex = stack[top--];
        
        if (graph->visited[currentVertex] == 0) {
            graph->visited[currentVertex] = 1;
            printf("Visited %d \n", currentVertex);
            
            // Push all adjacent vertices onto stack
            struct Node* temp = graph->adjLists[currentVertex];
            while (temp != NULL) {
                int adjVertex = temp->vertex;
                if (graph->visited[adjVertex] == 0) {
                    stack[++top] = adjVertex;
                }
                temp = temp->next;
            }
        }
    }
}

// Reset visited array for multiple traversals
void resetVisited(struct Graph* graph) {
    for (int i = 0; i < graph->numVertices; i++) {
        graph->visited[i] = 0;
    }
}

int main() {
    struct Graph* graph = createGraph(6);
    
    addEdge(graph, 0, 1);
    addEdge(graph, 0, 2);
    addEdge(graph, 1, 2);
    addEdge(graph, 1, 3);
    addEdge(graph, 2, 4);
    addEdge(graph, 3, 4);
    addEdge(graph, 4, 5);
    
    printf("Recursive DFS:\n");
    DFS_Recursive(graph, 0);
    
    resetVisited(graph);
    
    printf("\nIterative DFS:\n");
    DFS_Iterative(graph, 0);
    
    return 0;
}
```

## Explanation of the Code

1. **Graph Representation**: The graph is represented using adjacency lists. Each vertex has a linked list of its adjacent vertices.

2. **Node Structure**: Represents a vertex in the adjacency list with a `vertex` value and a `next` pointer.

3. **Graph Structure**: Contains:
   - `numVertices`: Total number of vertices
   - `adjLists`: Array of adjacency lists
   - `visited`: Array to track visited vertices

4. **Functions**:
   - `createNode`: Creates a new node for the adjacency list
   - `createGraph`: Initializes a graph with given number of vertices
   - `addEdge`: Adds an undirected edge between two vertices
   - `DFS_Recursive`: Implements DFS using recursion (implicit stack)
   - `DFS_Iterative`: Implements DFS using an explicit stack
   - `resetVisited`: Resets the visited array for multiple traversals

5. **Main Function**:
   - Creates a sample graph with 6 vertices
   - Adds edges between them
   - Performs both recursive and iterative DFS traversals
---
