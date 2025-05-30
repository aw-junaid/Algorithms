# Comprehensive Guide to Sets, Relations, Functions, Graphs, and Trees

## 1. Sets

### Definition
A set is a well-defined collection of distinct objects, considered as an object in its own right. The objects in a set are called elements or members.

### Basic Concepts
- **Cardinality**: Number of elements in a set (denoted |A| for set A)
- **Subset**: A is a subset of B (A ⊆ B) if all elements of A are in B
- **Power set**: Set of all subsets of a set
- **Universal set**: Set containing all objects under consideration

### Operations
- **Union (A ∪ B)**: Elements in A or B
- **Intersection (A ∩ B)**: Elements in both A and B
- **Difference (A \ B)**: Elements in A but not in B
- **Complement (A')**: Elements not in A (relative to universal set)

### C Implementation
```c
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    int *elements;
    int size;
} Set;

Set createSet(int arr[], int n) {
    Set s;
    s.elements = (int *)malloc(n * sizeof(int));
    
    // Remove duplicates
    int k = 0;
    for (int i = 0; i < n; i++) {
        int j;
        for (j = 0; j < k; j++) {
            if (s.elements[j] == arr[i]) break;
        }
        if (j == k) s.elements[k++] = arr[i];
    }
    
    s.size = k;
    return s;
}

Set unionSet(Set a, Set b) {
    Set result;
    result.elements = (int *)malloc((a.size + b.size) * sizeof(int));
    int k = 0;
    
    // Add all elements from a
    for (int i = 0; i < a.size; i++) {
        result.elements[k++] = a.elements[i];
    }
    
    // Add elements from b not already in result
    for (int i = 0; i < b.size; i++) {
        int found = 0;
        for (int j = 0; j < a.size; j++) {
            if (b.elements[i] == a.elements[j]) {
                found = 1;
                break;
            }
        }
        if (!found) result.elements[k++] = b.elements[i];
    }
    
    result.size = k;
    return result;
}

void printSet(Set s) {
    printf("{ ");
    for (int i = 0; i < s.size; i++) {
        printf("%d ", s.elements[i]);
    }
    printf("}\n");
}
```

## 2. Relations

### Definition
A relation from set A to set B is a subset of the Cartesian product A × B. For a relation R:
- **Domain**: Set of first elements in the ordered pairs
- **Range**: Set of second elements in the ordered pairs

### Types of Relations
1. **Reflexive**: (a,a) ∈ R for every a ∈ A
2. **Symmetric**: If (a,b) ∈ R then (b,a) ∈ R
3. **Transitive**: If (a,b) ∈ R and (b,c) ∈ R then (a,c) ∈ R
4. **Equivalence Relation**: Reflexive, symmetric, and transitive

### C Implementation
```c
#include <stdbool.h>

typedef struct {
    int a;
    int b;
} Pair;

typedef struct {
    Pair *pairs;
    int size;
} Relation;

bool isReflexive(Relation r, Set s) {
    for (int i = 0; i < s.size; i++) {
        int element = s.elements[i];
        bool found = false;
        for (int j = 0; j < r.size; j++) {
            if (r.pairs[j].a == element && r.pairs[j].b == element) {
                found = true;
                break;
            }
        }
        if (!found) return false;
    }
    return true;
}

bool isSymmetric(Relation r) {
    for (int i = 0; i < r.size; i++) {
        bool found = false;
        for (int j = 0; j < r.size; j++) {
            if (r.pairs[i].a == r.pairs[j].b && r.pairs[i].b == r.pairs[j].a) {
                found = true;
                break;
            }
        }
        if (!found) return false;
    }
    return true;
}
```

## 3. Functions

### Definition
A function f from set A to set B (f: A → B) is a relation that assigns to each element of A exactly one element of B.

### Types of Functions
1. **Injective (One-to-one)**: f(a) = f(b) implies a = b
2. **Surjective (Onto)**: For every b ∈ B, there exists a ∈ A such that f(a) = b
3. **Bijective**: Both injective and surjective

### C Implementation
```c
typedef struct {
    Pair *mapping; // Each pair represents (input, output)
    int size;
    Set domain;
    Set codomain;
} Function;

bool isFunction(Function f) {
    // Check each domain element appears exactly once
    for (int i = 0; i < f.domain.size; i++) {
        int count = 0;
        for (int j = 0; j < f.size; j++) {
            if (f.mapping[j].a == f.domain.elements[i]) count++;
        }
        if (count != 1) return false;
    }
    return true;
}

bool isInjective(Function f) {
    for (int i = 0; i < f.size; i++) {
        for (int j = i+1; j < f.size; j++) {
            if (f.mapping[i].b == f.mapping[j].b) {
                return false;
            }
        }
    }
    return true;
}
```

## 4. Graphs

### Definition
A graph G = (V, E) consists of:
- V: Set of vertices (nodes)
- E: Set of edges (pairs of vertices)

### Types of Graphs
1. **Undirected Graph**: Edges have no direction
2. **Directed Graph (Digraph)**: Edges have direction
3. **Weighted Graph**: Edges have weights
4. **Connected Graph**: Path exists between any two vertices
5. **Complete Graph**: Every pair of distinct vertices is connected

### Representations
1. **Adjacency Matrix**: 2D array where matrix[i][j] indicates edge between i and j
2. **Adjacency List**: Array of lists where each list stores adjacent vertices

### C Implementation (Adjacency List)
```c
typedef struct Node {
    int vertex;
    struct Node* next;
} Node;

typedef struct {
    Node** adjLists;
    int numVertices;
} Graph;

Node* createNode(int v) {
    Node* newNode = malloc(sizeof(Node));
    newNode->vertex = v;
    newNode->next = NULL;
    return newNode;
}

Graph createGraph(int vertices) {
    Graph graph;
    graph.numVertices = vertices;
    graph.adjLists = malloc(vertices * sizeof(Node*));
    
    for (int i = 0; i < vertices; i++) {
        graph.adjLists[i] = NULL;
    }
    
    return graph;
}

void addEdge(Graph* graph, int src, int dest) {
    // Add edge from src to dest
    Node* newNode = createNode(dest);
    newNode->next = graph->adjLists[src];
    graph->adjLists[src] = newNode;
    
    // For undirected graph, add edge from dest to src
    newNode = createNode(src);
    newNode->next = graph->adjLists[dest];
    graph->adjLists[dest] = newNode;
}

void BFS(Graph graph, int startVertex) {
    int* visited = calloc(graph.numVertices, sizeof(int));
    int* queue = malloc(graph.numVertices * sizeof(int));
    int front = 0, rear = 0;
    
    queue[rear++] = startVertex;
    visited[startVertex] = 1;
    
    while (front < rear) {
        int currentVertex = queue[front++];
        printf("%d ", currentVertex);
        
        Node* temp = graph.adjLists[currentVertex];
        while (temp) {
            int adjVertex = temp->vertex;
            if (!visited[adjVertex]) {
                queue[rear++] = adjVertex;
                visited[adjVertex] = 1;
            }
            temp = temp->next;
        }
    }
    free(visited);
    free(queue);
}
```

## 5. Trees

### Definition
A tree is an undirected graph that is connected and acyclic. Key properties:
- Any two vertices connected by exactly one path
- |E| = |V| - 1
- Adding any edge creates a cycle
- Removing any edge disconnects the graph

### Binary Tree
A tree where each node has at most two children (left and right).

### C Implementation
```c
typedef struct TreeNode {
    int data;
    struct TreeNode* left;
    struct TreeNode* right;
} TreeNode;

TreeNode* createNode(int data) {
    TreeNode* newNode = malloc(sizeof(TreeNode));
    newNode->data = data;
    newNode->left = NULL;
    newNode->right = NULL;
    return newNode;
}

void inorderTraversal(TreeNode* root) {
    if (root == NULL) return;
    inorderTraversal(root->left);
    printf("%d ", root->data);
    inorderTraversal(root->right);
}

void preorderTraversal(TreeNode* root) {
    if (root == NULL) return;
    printf("%d ", root->data);
    preorderTraversal(root->left);
    preorderTraversal(root->right);
}

void postorderTraversal(TreeNode* root) {
    if (root == NULL) return;
    postorderTraversal(root->left);
    postorderTraversal(root->right);
    printf("%d ", root->data);
}

int treeHeight(TreeNode* root) {
    if (root == NULL) return 0;
    int leftHeight = treeHeight(root->left);
    int rightHeight = treeHeight(root->right);
    return (leftHeight > rightHeight) ? leftHeight + 1 : rightHeight + 1;
}
```

---
