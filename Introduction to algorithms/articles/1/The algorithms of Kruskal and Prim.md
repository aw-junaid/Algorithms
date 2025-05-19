# Kruskal's and Prim's Algorithms for Minimum Spanning Trees

## Introduction

Kruskal's and Prim's algorithms are two fundamental greedy algorithms used to find a minimum spanning tree (MST) in a connected, undirected graph with weighted edges. A minimum spanning tree connects all the vertices together with the minimal total edge weight.

## Kruskal's Algorithm

### Explanation

Kruskal's algorithm works by sorting all the edges from the lowest weight to highest, then adding them to the MST one by one, ensuring that adding the edge doesn't form a cycle. It uses the Union-Find (Disjoint Set) data structure to efficiently check for cycles.

**Steps:**
1. Sort all edges in non-decreasing order of their weight
2. Initialize an empty MST
3. For each edge in the sorted list:
   - If adding the edge doesn't form a cycle, add it to the MST
   - Otherwise, discard it
4. Repeat until there are (V-1) edges in the MST (where V is number of vertices)

**Time Complexity:** O(E log E) or O(E log V) (due to sorting and Union-Find operations)
**Space Complexity:** O(V + E)

### C Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

// Structure to represent an edge
struct Edge {
    int src, dest, weight;
};

// Structure to represent a graph
struct Graph {
    int V, E;
    struct Edge* edge;
};

// Structure to represent a subset for union-find
struct subset {
    int parent;
    int rank;
};

// Create a graph with V vertices and E edges
struct Graph* createGraph(int V, int E) {
    struct Graph* graph = (struct Graph*)malloc(sizeof(struct Graph));
    graph->V = V;
    graph->E = E;
    graph->edge = (struct Edge*)malloc(E * sizeof(struct Edge));
    return graph;
}

// Find set of an element i (uses path compression)
int find(struct subset subsets[], int i) {
    if (subsets[i].parent != i)
        subsets[i].parent = find(subsets, subsets[i].parent);
    return subsets[i].parent;
}

// Union of two sets (uses union by rank)
void Union(struct subset subsets[], int x, int y) {
    int xroot = find(subsets, x);
    int yroot = find(subsets, y);

    if (subsets[xroot].rank < subsets[yroot].rank)
        subsets[xroot].parent = yroot;
    else if (subsets[xroot].rank > subsets[yroot].rank)
        subsets[yroot].parent = xroot;
    else {
        subsets[yroot].parent = xroot;
        subsets[xroot].rank++;
    }
}

// Compare function for qsort
int compare(const void* a, const void* b) {
    struct Edge* a1 = (struct Edge*)a;
    struct Edge* b1 = (struct Edge*)b;
    return a1->weight > b1->weight;
}

// Kruskal's algorithm
void KruskalMST(struct Graph* graph) {
    int V = graph->V;
    struct Edge result[V]; // Stores the resultant MST
    int e = 0; // Index for result[]
    int i = 0; // Index for sorted edges

    // Step 1: Sort all edges
    qsort(graph->edge, graph->E, sizeof(graph->edge[0]), compare);

    // Allocate memory for subsets
    struct subset* subsets = (struct subset*)malloc(V * sizeof(struct subset));

    // Create V subsets with single elements
    for (int v = 0; v < V; v++) {
        subsets[v].parent = v;
        subsets[v].rank = 0;
    }

    // Number of edges to be taken is V-1
    while (e < V - 1 && i < graph->E) {
        // Step 2: Pick the smallest edge
        struct Edge next_edge = graph->edge[i++];

        int x = find(subsets, next_edge.src);
        int y = find(subsets, next_edge.dest);

        // If including this edge doesn't cause a cycle
        if (x != y) {
            result[e++] = next_edge;
            Union(subsets, x, y);
        }
    }

    // Print the MST
    printf("Edges in the MST (Kruskal's algorithm):\n");
    int minimumCost = 0;
    for (i = 0; i < e; ++i) {
        printf("%d -- %d == %d\n", result[i].src, result[i].dest, result[i].weight);
        minimumCost += result[i].weight;
    }
    printf("Minimum Cost Spanning Tree: %d\n", minimumCost);
}

// Driver code
int main() {
    int V = 4; // Number of vertices
    int E = 5; // Number of edges
    struct Graph* graph = createGraph(V, E);

    // Add edges
    graph->edge[0].src = 0;
    graph->edge[0].dest = 1;
    graph->edge[0].weight = 10;

    graph->edge[1].src = 0;
    graph->edge[1].dest = 2;
    graph->edge[1].weight = 6;

    graph->edge[2].src = 0;
    graph->edge[2].dest = 3;
    graph->edge[2].weight = 5;

    graph->edge[3].src = 1;
    graph->edge[3].dest = 3;
    graph->edge[3].weight = 15;

    graph->edge[4].src = 2;
    graph->edge[4].dest = 3;
    graph->edge[4].weight = 4;

    KruskalMST(graph);

    return 0;
}
```

## Prim's Algorithm

### Explanation

Prim's algorithm grows the MST one vertex at a time, starting from an arbitrary vertex. At each step, it adds the cheapest edge that connects a vertex in the MST to a vertex outside the MST.

**Steps:**
1. Initialize a tree with a single vertex (chosen arbitrarily)
2. Grow the tree by one edge: of all edges that connect the tree to vertices not yet in the tree, find the minimum-weight edge, and transfer it to the tree
3. Repeat step 2 until all vertices are in the tree

**Time Complexity:** O(V²) for adjacency matrix, O(E log V) with binary heap and adjacency list
**Space Complexity:** O(V + E)

### C Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>
#include <stdbool.h>

// Number of vertices in the graph
#define V 5

// Function to find the vertex with minimum key value
int minKey(int key[], bool mstSet[]) {
    int min = INT_MAX, min_index;

    for (int v = 0; v < V; v++)
        if (mstSet[v] == false && key[v] < min)
            min = key[v], min_index = v;

    return min_index;
}

// Function to print the constructed MST
void printMST(int parent[], int graph[V][V]) {
    printf("Edges in the MST (Prim's algorithm):\n");
    int minimumCost = 0;
    for (int i = 1; i < V; i++) {
        printf("%d -- %d == %d\n", parent[i], i, graph[i][parent[i]]);
        minimumCost += graph[i][parent[i]];
    }
    printf("Minimum Cost Spanning Tree: %d\n", minimumCost);
}

// Function to construct and print MST using Prim's algorithm
void primMST(int graph[V][V]) {
    int parent[V]; // Array to store constructed MST
    int key[V];    // Key values used to pick minimum weight edge
    bool mstSet[V]; // To represent set of vertices included in MST

    // Initialize all keys as INFINITE and mstSet[] as false
    for (int i = 0; i < V; i++)
        key[i] = INT_MAX, mstSet[i] = false;

    // Always include first vertex in MST
    key[0] = 0; // Make key 0 so this vertex is picked first
    parent[0] = -1; // First node is always root of MST

    // The MST will have V vertices
    for (int count = 0; count < V - 1; count++) {
        // Pick the minimum key vertex from the set of vertices not yet in MST
        int u = minKey(key, mstSet);

        // Add the picked vertex to the MST set
        mstSet[u] = true;

        // Update key value and parent index of adjacent vertices
        for (int v = 0; v < V; v++)
            // graph[u][v] is non-zero only for adjacent vertices
            // mstSet[v] is false for vertices not yet in MST
            // Update key only if graph[u][v] is smaller than key[v]
            if (graph[u][v] && mstSet[v] == false && graph[u][v] < key[v])
                parent[v] = u, key[v] = graph[u][v];
    }

    // Print the constructed MST
    printMST(parent, graph);
}

// Driver code
int main() {
    /* Let us create the following graph
          2    3
      (0)--(1)--(2)
       |   / \   |
      6| 8/   \5 |7
       | /     \ |
      (3)-------(4)
            9          */
    int graph[V][V] = { { 0, 2, 0, 6, 0 },
                        { 2, 0, 3, 8, 5 },
                        { 0, 3, 0, 0, 7 },
                        { 6, 8, 0, 0, 9 },
                        { 0, 5, 7, 9, 0 } };

    primMST(graph);

    return 0;
}
```

## Key Differences Between Kruskal's and Prim's Algorithms

1. **Approach**:
   - Kruskal's: Edge-based, adds edges in increasing weight
   - Prim's: Vertex-based, grows the MST from a starting vertex

2. **Data Structures**:
   - Kruskal's: Uses Union-Find (Disjoint Set) to detect cycles
   - Prim's: Uses priority queue (or simple array) to select next edge

3. **Best for**:
   - Kruskal's: Sparse graphs (few edges)
   - Prim's: Dense graphs (many edges)

4. **Initialization**:
   - Kruskal's: Needs all edges sorted
   - Prim's: Can start with any vertex

Both algorithms are guaranteed to find the minimum spanning tree for any connected, undirected graph with weighted edges, but their efficiency differs based on graph density.
