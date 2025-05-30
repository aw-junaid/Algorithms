# Approximation Algorithms: Key Problems and Implementations

Approximation algorithms are essential for solving NP-hard problems where finding an exact solution is computationally infeasible. Here's a detailed explanation of the problems you mentioned along with C implementations.

## 1. Vertex-Cover Problem

### Problem Definition
Given an undirected graph G=(V,E), a vertex cover is a subset V' ⊆ V such that every edge in E has at least one endpoint in V'. The goal is to find a vertex cover of minimum size.

### Approximation Algorithm (2-approximation)
1. Initialize an empty vertex cover C
2. While there are edges remaining in E:
   a. Pick any edge (u,v) ∈ E
   b. Add both u and v to C
   c. Remove all edges incident to u or v from E
3. Return C

This algorithm guarantees that the solution is at most twice the optimal solution.

### C Implementation
```c
#include <stdio.h>
#include <stdbool.h>
#include <stdlib.h>

#define MAX_VERTICES 100

// Adjacency matrix representation
bool graph[MAX_VERTICES][MAX_VERTICES];
bool in_cover[MAX_VERTICES];

void vertex_cover_approximation(int vertices) {
    for (int i = 0; i < vertices; i++) {
        in_cover[i] = false;
    }
    
    for (int u = 0; u < vertices; u++) {
        for (int v = 0; v < vertices; v++) {
            if (graph[u][v] && !in_cover[u] && !in_cover[v]) {
                in_cover[u] = true;
                in_cover[v] = true;
                break;
            }
        }
    }
    
    printf("Vertex Cover: ");
    for (int i = 0; i < vertices; i++) {
        if (in_cover[i]) {
            printf("%d ", i);
        }
    }
    printf("\n");
}

int main() {
    int vertices, edges, u, v;
    
    printf("Enter number of vertices: ");
    scanf("%d", &vertices);
    
    printf("Enter number of edges: ");
    scanf("%d", &edges);
    
    printf("Enter edges (u v):\n");
    for (int i = 0; i < edges; i++) {
        scanf("%d %d", &u, &v);
        graph[u][v] = graph[v][u] = true;
    }
    
    vertex_cover_approximation(vertices);
    
    return 0;
}
```

## 2. Traveling-Salesman Problem (TSP)

### Problem Definition
Given a complete undirected graph with non-negative edge costs, find a minimum-cost cycle that visits every vertex exactly once.

### Approximation Algorithm (Metric TSP)
For metric TSP (where triangle inequality holds), we can use a 2-approximation:
1. Find a minimum spanning tree (MST) T of the graph
2. Duplicate all edges of T to create an Eulerian graph
3. Find an Eulerian tour in this graph
4. Convert to Hamiltonian cycle by shortcutting

### C Implementation
```c
#include <stdio.h>
#include <limits.h>
#include <stdbool.h>

#define MAX_VERTICES 100

int graph[MAX_VERTICES][MAX_VERTICES];
int parent[MAX_VERTICES];

int find(int i) {
    while (parent[i] != i) {
        i = parent[i];
    }
    return i;
}

void union1(int i, int j) {
    int a = find(i);
    int b = find(j);
    parent[a] = b;
}

void kruskalMST(int vertices) {
    int mincost = 0;
    
    for (int i = 0; i < vertices; i++) {
        parent[i] = i;
    }
    
    int edge_count = 0;
    while (edge_count < vertices - 1) {
        int min = INT_MAX, a = -1, b = -1;
        for (int i = 0; i < vertices; i++) {
            for (int j = 0; j < vertices; j++) {
                if (find(i) != find(j) && graph[i][j] < min) {
                    min = graph[i][j];
                    a = i;
                    b = j;
                }
            }
        }
        
        union1(a, b);
        printf("Edge %d:(%d, %d) cost:%d\n", edge_count++, a, b, min);
        mincost += min;
    }
    printf("\nMinimum cost= %d\n", mincost);
}

void tsp_approximation(int vertices) {
    // Simplified approach - using MST as approximation
    printf("Approximate TSP solution using MST:\n");
    kruskalMST(vertices);
}

int main() {
    int vertices;
    
    printf("Enter number of vertices: ");
    scanf("%d", &vertices);
    
    printf("Enter adjacency matrix (use INT_MAX for no edge):\n");
    for (int i = 0; i < vertices; i++) {
        for (int j = 0; j < vertices; j++) {
            scanf("%d", &graph[i][j]);
        }
    }
    
    tsp_approximation(vertices);
    
    return 0;
}
```

## 3. Set-Covering Problem

### Problem Definition
Given a universe U of n elements, a collection of subsets S₁, S₂, ..., Sₘ of U, and a cost function c: S → ℝ⁺, find a minimum-cost subcollection of S that covers all elements of U.

### Greedy Approximation Algorithm
1. Initialize the uncovered set to U
2. While uncovered set is not empty:
   a. Choose the set Sᵢ with minimum cost per uncovered element (cost/|Sᵢ ∩ uncovered|)
   b. Add Sᵢ to the cover
   c. Remove elements of Sᵢ from uncovered set
3. Return the selected sets

This gives a Hₙ-approximation where Hₙ is the n-th harmonic number.

### C Implementation
```c
#include <stdio.h>
#include <stdbool.h>
#include <limits.h>

#define MAX_ELEMENTS 100
#define MAX_SETS 100

bool sets[MAX_SETS][MAX_ELEMENTS];
int costs[MAX_SETS];
bool covered[MAX_ELEMENTS];
bool selected[MAX_SETS];

void set_cover_approximation(int num_elements, int num_sets) {
    for (int i = 0; i < num_elements; i++) {
        covered[i] = false;
    }
    for (int i = 0; i < num_sets; i++) {
        selected[i] = false;
    }
    
    int remaining = num_elements;
    while (remaining > 0) {
        int best_set = -1;
        double best_ratio = INT_MAX;
        
        for (int i = 0; i < num_sets; i++) {
            if (!selected[i]) {
                int uncovered = 0;
                for (int j = 0; j < num_elements; j++) {
                    if (sets[i][j] && !covered[j]) {
                        uncovered++;
                    }
                }
                if (uncovered > 0) {
                    double ratio = (double)costs[i] / uncovered;
                    if (ratio < best_ratio) {
                        best_ratio = ratio;
                        best_set = i;
                    }
                }
            }
        }
        
        if (best_set == -1) break; // No solution
        
        selected[best_set] = true;
        for (int j = 0; j < num_elements; j++) {
            if (sets[best_set][j] && !covered[j]) {
                covered[j] = true;
                remaining--;
            }
        }
    }
    
    printf("Selected sets: ");
    for (int i = 0; i < num_sets; i++) {
        if (selected[i]) {
            printf("%d ", i);
        }
    }
    printf("\n");
}

int main() {
    int num_elements, num_sets;
    
    printf("Enter number of elements: ");
    scanf("%d", &num_elements);
    
    printf("Enter number of sets: ");
    scanf("%d", &num_sets);
    
    printf("Enter sets (1 if element is in set, 0 otherwise):\n");
    for (int i = 0; i < num_sets; i++) {
        printf("Set %d: ", i);
        for (int j = 0; j < num_elements; j++) {
            scanf("%d", &sets[i][j]);
        }
    }
    
    printf("Enter costs for each set:\n");
    for (int i = 0; i < num_sets; i++) {
        scanf("%d", &costs[i]);
    }
    
    set_cover_approximation(num_elements, num_sets);
    
    return 0;
}
```

## 4. Subset-Sum Problem

### Problem Definition
Given a set of integers and a target sum, determine whether there's a subset that adds up to the target sum.

### Approximation Scheme
For the optimization version (find subset with sum ≤ target and as large as possible), we can use a dynamic programming approach with scaling to get an FPTAS (fully polynomial-time approximation scheme).

### C Implementation
```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

#define MAX_SIZE 100

int max(int a, int b) {
    return (a > b) ? a : b;
}

void subset_sum_approximation(int set[], int n, int target, double epsilon) {
    int max_val = 0;
    for (int i = 0; i < n; i++) {
        if (set[i] > max_val) {
            max_val = set[i];
        }
    }
    
    int k = (int)(epsilon * max_val / n);
    if (k == 0) k = 1;
    
    int scaled_target = target / k;
    int* scaled_set = (int*)malloc(n * sizeof(int));
    for (int i = 0; i < n; i++) {
        scaled_set[i] = set[i] / k;
    }
    
    // DP table
    int** dp = (int**)malloc((n + 1) * sizeof(int*));
    for (int i = 0; i <= n; i++) {
        dp[i] = (int*)malloc((scaled_target + 1) * sizeof(int));
    }
    
    for (int i = 0; i <= n; i++) {
        for (int j = 0; j <= scaled_target; j++) {
            if (j == 0) {
                dp[i][j] = 1;
            } else if (i == 0) {
                dp[i][j] = 0;
            } else if (scaled_set[i - 1] <= j) {
                dp[i][j] = dp[i - 1][j] || dp[i - 1][j - scaled_set[i - 1]];
            } else {
                dp[i][j] = dp[i - 1][j];
            }
        }
    }
    
    // Find the largest sum achievable
    int result = 0;
    for (int j = scaled_target; j >= 0; j--) {
        if (dp[n][j]) {
            result = j * k;
            break;
        }
    }
    
    printf("Approximate maximum sum <= target: %d\n", result);
    
    // Free memory
    for (int i = 0; i <= n; i++) {
        free(dp[i]);
    }
    free(dp);
    free(scaled_set);
}

int main() {
    int set[MAX_SIZE], n, target;
    double epsilon;
    
    printf("Enter number of elements: ");
    scanf("%d", &n);
    
    printf("Enter elements: ");
    for (int i = 0; i < n; i++) {
        scanf("%d", &set[i]);
    }
    
    printf("Enter target sum: ");
    scanf("%d", &target);
    
    printf("Enter epsilon (approximation parameter, 0 < epsilon < 1): ");
    scanf("%lf", &epsilon);
    
    subset_sum_approximation(set, n, target, epsilon);
    
    return 0;
}
```

## 5. Randomization and Linear Programming

Randomized algorithms use randomness as part of their logic. Linear programming is a method to achieve the best outcome in a mathematical model whose requirements are represented by linear relationships.

### Example: Randomized Rounding for Set Cover
1. Formulate the set cover problem as an integer linear program (ILP)
2. Solve the linear programming relaxation (allow fractional values)
3. Randomly round the fractional solutions to integers based on their values

### C Implementation (Randomized Rounding)
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <time.h>

#define MAX_ELEMENTS 100
#define MAX_SETS 100

bool sets[MAX_SETS][MAX_ELEMENTS];
double lp_solution[MAX_SETS];
bool covered[MAX_ELEMENTS];
bool selected[MAX_SETS];

void randomized_rounding(int num_elements, int num_sets) {
    srand(time(NULL));
    
    for (int i = 0; i < num_elements; i++) {
        covered[i] = false;
    }
    for (int i = 0; i < num_sets; i++) {
        selected[i] = false;
    }
    
    // Simulate getting LP solution (in practice, you'd use an LP solver)
    // Here we assume lp_solution is already computed
    
    // Randomized rounding
    for (int i = 0; i < num_sets; i++) {
        double r = (double)rand() / RAND_MAX;
        if (r < lp_solution[i]) {
            selected[i] = true;
            for (int j = 0; j < num_elements; j++) {
                if (sets[i][j]) {
                    covered[j] = true;
                }
            }
        }
    }
    
    // Verify all elements are covered
    bool all_covered = true;
    for (int j = 0; j < num_elements; j++) {
        if (!covered[j]) {
            all_covered = false;
            break;
        }
    }
    
    if (!all_covered) {
        // If not all covered, add sets greedily
        for (int i = 0; i < num_sets; i++) {
            if (!selected[i]) {
                bool has_uncovered = false;
                for (int j = 0; j < num_elements; j++) {
                    if (sets[i][j] && !covered[j]) {
                        has_uncovered = true;
                        break;
                    }
                }
                if (has_uncovered) {
                    selected[i] = true;
                    for (int j = 0; j < num_elements; j++) {
                        if (sets[i][j]) {
                            covered[j] = true;
                        }
                    }
                }
            }
        }
    }
    
    printf("Selected sets: ");
    for (int i = 0; i < num_sets; i++) {
        if (selected[i]) {
            printf("%d ", i);
        }
    }
    printf("\n");
}

int main() {
    int num_elements, num_sets;
    
    printf("Enter number of elements: ");
    scanf("%d", &num_elements);
    
    printf("Enter number of sets: ");
    scanf("%d", &num_sets);
    
    printf("Enter sets (1 if element is in set, 0 otherwise):\n");
    for (int i = 0; i < num_sets; i++) {
        printf("Set %d: ", i);
        for (int j = 0; j < num_elements; j++) {
            scanf("%d", &sets[i][j]);
        }
    }
    
    printf("Enter LP solution (fractional values between 0 and 1):\n");
    for (int i = 0; i < num_sets; i++) {
        scanf("%lf", &lp_solution[i]);
    }
    
    randomized_rounding(num_elements, num_sets);
    
    return 0;
}
```

---
