# Maximum Flow: Comprehensive Explanation and Implementation

## 1. Flow Networks

A flow network is a directed graph G = (V, E) where:
- Each edge (u, v) ∈ E has a non-negative capacity c(u, v) ≥ 0
- There are two special vertices: source (s) and sink (t)
- If (u, v) ∉ E, then c(u, v) = 0
- No self-loops allowed

**Flow** in a network is a function f: V × V → ℝ that satisfies:
1. **Capacity constraint**: 0 ≤ f(u, v) ≤ c(u, v) for all u, v ∈ V
2. **Flow conservation**: For all u ∈ V - {s, t}, ∑ f(v, u) = ∑ f(u, v) (inflow = outflow)

The **value of flow** |f| is the total flow out of source minus flow into source.

## 2. The Ford-Fulkerson Method

This method finds the maximum flow in a flow network. It works by iteratively finding augmenting paths in the residual network and augmenting the flow along these paths.

### Key Concepts:
- **Residual network**: G_f = (V, E_f) where E_f contains edges with positive residual capacity c_f(u, v) = c(u, v) - f(u, v)
- **Augmenting path**: A simple path from s to t in the residual network
- **Residual capacity**: The maximum amount we can increase the flow along the augmenting path

### Algorithm Steps:
1. Initialize flow f to 0
2. While there exists an augmenting path p in residual network G_f:
   - Find c_f(p) = min{c_f(u, v) : (u, v) is in p}
   - For each edge (u, v) in p:
     - f(u, v) += c_f(p)
     - f(v, u) -= c_f(p)

### Implementation (C code):

```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>
#include <stdbool.h>

#define V 6 // Number of vertices in the graph

// Using BFS as a searching algorithm 
bool bfs(int rGraph[V][V], int s, int t, int parent[]) {
    bool visited[V];
    for (int i = 0; i < V; i++)
        visited[i] = false;

    int queue[V];
    int front = 0, rear = 0;
    queue[rear++] = s;
    visited[s] = true;
    parent[s] = -1;

    while (front < rear) {
        int u = queue[front++];
        for (int v = 0; v < V; v++) {
            if (!visited[v] && rGraph[u][v] > 0) {
                if (v == t) {
                    parent[v] = u;
                    return true;
                }
                queue[rear++] = v;
                parent[v] = u;
                visited[v] = true;
            }
        }
    }
    return false;
}

// Ford-Fulkerson algorithm
int fordFulkerson(int graph[V][V], int s, int t) {
    int u, v;
    int rGraph[V][V]; // Residual graph
    
    for (u = 0; u < V; u++)
        for (v = 0; v < V; v++)
            rGraph[u][v] = graph[u][v];

    int parent[V]; // Stores path
    int max_flow = 0;

    while (bfs(rGraph, s, t, parent)) {
        int path_flow = INT_MAX;
        for (v = t; v != s; v = parent[v]) {
            u = parent[v];
            path_flow = (path_flow < rGraph[u][v]) ? path_flow : rGraph[u][v];
        }

        for (v = t; v != s; v = parent[v]) {
            u = parent[v];
            rGraph[u][v] -= path_flow;
            rGraph[v][u] += path_flow;
        }

        max_flow += path_flow;
    }
    return max_flow;
}

int main() {
    int graph[V][V] = { {0, 16, 13, 0, 0, 0},
                        {0, 0, 10, 12, 0, 0},
                        {0, 4, 0, 0, 14, 0},
                        {0, 0, 9, 0, 0, 20},
                        {0, 0, 0, 7, 0, 4},
                        {0, 0, 0, 0, 0, 0} };

    printf("The maximum possible flow is %d\n", fordFulkerson(graph, 0, 5));
    return 0;
}
```

## 3. Maximum Bipartite Matching

A bipartite graph is a graph whose vertices can be divided into two disjoint sets L and R such that every edge connects a vertex in L to one in R.

A matching is a subset of edges where no two edges share a common vertex. A maximum matching is a matching of maximum size.

### Reduction to Maximum Flow:
1. Add source s connected to all vertices in L
2. Add sink t connected from all vertices in R
3. Set all edge capacities to 1
4. The maximum flow in this network equals the maximum matching

### Implementation (C code):

```c
#include <stdio.h>
#include <stdbool.h>
#include <string.h>

#define M 6 // Number of applicants
#define N 6 // Number of jobs

bool bpm(bool bpGraph[M][N], int u, bool seen[], int matchR[]) {
    for (int v = 0; v < N; v++) {
        if (bpGraph[u][v] && !seen[v]) {
            seen[v] = true;
            if (matchR[v] < 0 || bpm(bpGraph, matchR[v], seen, matchR)) {
                matchR[v] = u;
                return true;
            }
        }
    }
    return false;
}

int maxBPM(bool bpGraph[M][N]) {
    int matchR[N];
    memset(matchR, -1, sizeof(matchR));
    int result = 0;
    for (int u = 0; u < M; u++) {
        bool seen[N];
        memset(seen, 0, sizeof(seen));
        if (bpm(bpGraph, u, seen, matchR))
            result++;
    }
    return result;
}

int main() {
    bool bpGraph[M][N] = { {0, 1, 1, 0, 0, 0},
                          {1, 0, 0, 1, 0, 0},
                          {0, 0, 1, 0, 0, 0},
                          {0, 0, 1, 1, 0, 0},
                          {0, 0, 0, 0, 0, 0},
                          {0, 0, 0, 0, 0, 1} };

    printf("Maximum number of applicants that can get job is %d\n", maxBPM(bpGraph));
    return 0;
}
```

## 4. Push-Relabel Algorithms

These are more efficient algorithms for computing maximum flow, especially for dense graphs. They work by maintaining a preflow (which may violate conservation constraints) and gradually converting it into a valid flow.

### Key Concepts:
- **Preflow**: Like flow but allows more inflow than outflow (except at source)
- **Height function**: h(v) gives height of vertex v
- **Excess flow**: e(u) = inflow - outflow at u
- **Push operation**: Move flow from u to v if h(u) > h(v) and residual capacity exists
- **Relabel operation**: Increase h(u) if u has excess flow but no admissible edges

### Implementation (C code):

```c
#include <stdio.h>
#include <limits.h>
#include <stdbool.h>

#define V 6

struct Vertex {
    int h, e;
};

struct Edge {
    int u, v, capacity, flow;
};

void initializePreflow(int graph[V][V], struct Vertex vertices[V], int s) {
    for (int i = 0; i < V; i++) {
        vertices[i].h = 0;
        vertices[i].e = 0;
    }
    
    vertices[s].h = V;
    for (int v = 0; v < V; v++) {
        if (graph[s][v] > 0) {
            vertices[v].e = graph[s][v];
            graph[s][v] = 0;
            graph[v][s] += vertices[v].e;
        }
    }
}

void push(int u, int v, int graph[V][V], struct Vertex vertices[V]) {
    int delta = (vertices[u].e < graph[u][v]) ? vertices[u].e : graph[u][v];
    graph[u][v] -= delta;
    graph[v][u] += delta;
    vertices[u].e -= delta;
    vertices[v].e += delta;
}

void relabel(int u, int graph[V][V], struct Vertex vertices[V]) {
    int min_h = INT_MAX;
    for (int v = 0; v < V; v++) {
        if (graph[u][v] > 0) {
            if (vertices[v].h < min_h)
                min_h = vertices[v].h;
        }
    }
    vertices[u].h = min_h + 1;
}

int pushRelabel(int graph[V][V], int s, int t) {
    struct Vertex vertices[V];
    initializePreflow(graph, vertices, s);
    
    bool done = false;
    while (!done) {
        done = true;
        for (int u = 0; u < V; u++) {
            if (u == t || u == s) continue;
            if (vertices[u].e > 0) {
                done = false;
                bool pushed = false;
                for (int v = 0; v < V && !pushed; v++) {
                    if (graph[u][v] > 0 && vertices[u].h == vertices[v].h + 1) {
                        push(u, v, graph, vertices);
                        pushed = true;
                    }
                }
                if (!pushed) {
                    relabel(u, graph, vertices);
                }
            }
        }
    }
    return vertices[t].e;
}

int main() {
    int graph[V][V] = { {0, 16, 13, 0, 0, 0},
                        {0, 0, 10, 12, 0, 0},
                        {0, 4, 0, 0, 14, 0},
                        {0, 0, 9, 0, 0, 20},
                        {0, 0, 0, 7, 0, 4},
                        {0, 0, 0, 0, 0, 0} };

    printf("Maximum flow is %d\n", pushRelabel(graph, 0, 5));
    return 0;
}
```

## 5. The Relabel-to-Front Algorithm

This is an efficient implementation of push-relabel that maintains a list of vertices with excess flow and processes them in a specific order.

### Key Features:
- Maintains a "discharge" operation that repeatedly pushes/relabels a vertex until it has no excess
- Uses a "neighbor list" for each vertex
- Processes vertices in a specific order (front of the list)

### Implementation (C code):

```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>
#include <stdbool.h>

#define V 6

struct Vertex {
    int h, e;
    struct Node* current; // Current neighbor
    struct Node* neighbors; // List of neighbors
};

struct Node {
    int v;
    struct Node* next;
};

struct Edge {
    int capacity, flow;
};

void initializePreflow(struct Edge graph[V][V], struct Vertex vertices[V], int s) {
    for (int i = 0; i < V; i++) {
        vertices[i].h = 0;
        vertices[i].e = 0;
        vertices[i].current = vertices[i].neighbors;
    }
    
    vertices[s].h = V;
    for (int v = 0; v < V; v++) {
        if (graph[s][v].capacity > 0) {
            graph[s][v].flow = graph[s][v].capacity;
            graph[v][s].flow = -graph[s][v].flow;
            vertices[v].e = graph[s][v].flow;
            vertices[s].e -= graph[s][v].flow;
        }
    }
}

void push(struct Edge graph[V][V], struct Vertex vertices[V], int u, int v) {
    int delta = (vertices[u].e < (graph[u][v].capacity - graph[u][v].flow)) ? 
                vertices[u].e : (graph[u][v].capacity - graph[u][v].flow);
    graph[u][v].flow += delta;
    graph[v][u].flow -= delta;
    vertices[u].e -= delta;
    vertices[v].e += delta;
}

void relabel(struct Edge graph[V][V], struct Vertex vertices[V], int u) {
    int min_h = INT_MAX;
    struct Node* node = vertices[u].neighbors;
    while (node != NULL) {
        int v = node->v;
        if (graph[u][v].capacity - graph[u][v].flow > 0) {
            if (vertices[v].h < min_h)
                min_h = vertices[v].h;
        }
        node = node->next;
    }
    vertices[u].h = min_h + 1;
}

void discharge(struct Edge graph[V][V], struct Vertex vertices[V], int u) {
    while (vertices[u].e > 0) {
        struct Node* node = vertices[u].current;
        if (node == NULL) {
            relabel(graph, vertices, u);
            vertices[u].current = vertices[u].neighbors;
        } else {
            int v = node->v;
            if ((graph[u][v].capacity - graph[u][v].flow > 0) && 
                (vertices[u].h == vertices[v].h + 1)) {
                push(graph, vertices, u, v);
            } else {
                vertices[u].current = node->next;
            }
        }
    }
}

int relabelToFront(struct Edge graph[V][V], int s, int t) {
    struct Vertex vertices[V];
    
    // Initialize neighbor lists
    for (int u = 0; u < V; u++) {
        vertices[u].neighbors = NULL;
        for (int v = 0; v < V; v++) {
            if (graph[u][v].capacity > 0 || graph[v][u].capacity > 0) {
                struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
                newNode->v = v;
                newNode->next = vertices[u].neighbors;
                vertices[u].neighbors = newNode;
            }
        }
    }
    
    initializePreflow(graph, vertices, s);
    
    int list[V-2]; // List of vertices except s and t
    int count = 0;
    for (int u = 0; u < V; u++) {
        if (u != s && u != t) {
            list[count++] = u;
        }
    }
    
    int i = 0;
    while (i < V-2) {
        int u = list[i];
        int old_height = vertices[u].h;
        discharge(graph, vertices, u);
        if (vertices[u].h > old_height) {
            // Move u to front of list
            for (int j = i; j > 0; j--) {
                list[j] = list[j-1];
            }
            list[0] = u;
            i = 0; // Start over
        } else {
            i++;
        }
    }
    
    return vertices[t].e;
}

int main() {
    struct Edge graph[V][V];
    // Initialize all edges to 0
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            graph[i][j].capacity = 0;
            graph[i][j].flow = 0;
        }
    }
    
    // Create the same graph as before
    graph[0][1].capacity = 16;
    graph[0][2].capacity = 13;
    graph[1][2].capacity = 10;
    graph[1][3].capacity = 12;
    graph[2][1].capacity = 4;
    graph[2][4].capacity = 14;
    graph[3][2].capacity = 9;
    graph[3][5].capacity = 20;
    graph[4][3].capacity = 7;
    graph[4][5].capacity = 4;
    
    printf("Maximum flow is %d\n", relabelToFront(graph, 0, 5));
    return 0;
}
```

## Comparison of Algorithms

1. **Ford-Fulkerson**:
   - Simple to understand and implement
   - Time complexity: O(E * max_flow) (can be bad for large flows)
   - Uses BFS (Edmonds-Karp variant) for O(VE²) time

2. **Push-Relabel**:
   - More complex but more efficient in practice
   - Time complexity: O(V²E)
   - Better for dense graphs

3. **Relabel-to-Front**:
   - Variant of push-relabel with O(V³) time
   - Maintains list of vertices to process more efficiently

Each algorithm has its use cases depending on the nature of the graph and the expected flow values.
