# Linear Programming: A Comprehensive Guide

## Introduction to Linear Programming

Linear programming (LP) is a mathematical method for determining the best possible outcome in a given mathematical model with linear relationships. It's widely used in business, economics, engineering, and military applications.

## Standard Form and Slack Forms

### Standard Form

A linear program in **standard form** must satisfy:
1. Maximization problem (can convert minimization by negating)
2. All constraints are inequalities of the "≤" type
3. All variables are non-negative

Mathematically, it's expressed as:
```
Maximize: cᵀx
Subject to: Ax ≤ b
            x ≥ 0
```
Where:
- `c` is the coefficient vector of the objective function
- `A` is the constraint matrix
- `b` is the right-hand side vector
- `x` is the vector of decision variables

### Slack Form

To use the simplex algorithm, we convert the standard form to **slack form** by introducing slack variables to turn inequalities into equalities.

For a constraint `a₁x₁ + a₂x₂ + ... + aₙxₙ ≤ b`, we add a slack variable `s`:
`a₁x₁ + a₂x₂ + ... + aₙxₙ + s = b` where `s ≥ 0`

## Formulating Problems as Linear Programs

Common steps to formulate a problem as an LP:
1. Identify decision variables
2. Formulate the objective function (maximize or minimize)
3. Formulate the constraints
4. Specify non-negativity constraints

### Example: Production Problem

**Problem:** A company produces two products (A and B). Product A gives $3 profit, B gives $5. Production constraints:
- Machine time: 2A + 4B ≤ 100 hours
- Labor: 3A + 2B ≤ 80 hours
- Storage: A + B ≤ 30 units

**LP Formulation:**
```
Maximize: 3A + 5B
Subject to:
  2A + 4B ≤ 100
  3A + 2B ≤ 80
  A + B ≤ 30
  A, B ≥ 0
```

## The Simplex Algorithm

The simplex method is an iterative procedure for solving LP problems. It moves from one basic feasible solution to another while improving the objective function value.

### Algorithm Steps:

1. Convert the LP to slack form
2. Find an initial basic feasible solution (BFS)
3. While the current solution is not optimal:
   a. Choose an entering variable (nonbasic variable that can improve the objective)
   b. Choose a leaving variable (basic variable that limits the increase)
   c. Pivot (swap entering and leaving variables)
4. When no improving variables exist, the current solution is optimal

### C Implementation of Simplex Algorithm

```c
#include <stdio.h>
#include <stdbool.h>
#include <math.h>

#define MAX_CONSTRAINTS 10
#define MAX_VARIABLES 10

typedef struct {
    double A[MAX_CONSTRAINTS][MAX_VARIABLES + MAX_CONSTRAINTS];
    double b[MAX_CONSTRAINTS];
    double c[MAX_VARIABLES + MAX_CONSTRAINTS];
    int num_vars;
    int num_constraints;
    int basis[MAX_CONSTRAINTS]; // indices of basic variables
} LPProblem;

void print_tableau(LPProblem *p) {
    printf("Current tableau:\n");
    for (int i = 0; i < p->num_constraints; i++) {
        for (int j = 0; j < p->num_vars + p->num_constraints; j++) {
            printf("%6.2f ", p->A[i][j]);
        }
        printf(" | %6.2f\n", p->b[i]);
    }
    printf("Obj: ");
    for (int j = 0; j < p->num_vars + p->num_constraints; j++) {
        printf("%6.2f ", p->c[j]);
    }
    printf("\n\n");
}

bool simplex(LPProblem *p, double *solution) {
    int m = p->num_constraints;
    int n = p->num_vars;
    int total_vars = n + m;
    
    // Initialize basis to slack variables
    for (int i = 0; i < m; i++) {
        p->basis[i] = n + i;
    }
    
    while (true) {
        print_tableau(p);
        
        // Check for optimality
        bool optimal = true;
        int entering = -1;
        double max_reduction = 0.0;
        
        for (int j = 0; j < total_vars; j++) {
            bool is_basic = false;
            for (int i = 0; i < m; i++) {
                if (p->basis[i] == j) {
                    is_basic = true;
                    break;
                }
            }
            
            if (!is_basic && p->c[j] > max_reduction) {
                max_reduction = p->c[j];
                entering = j;
                optimal = false;
            }
        }
        
        if (optimal) {
            break; // Current solution is optimal
        }
        
        if (entering == -1) {
            printf("Problem is unbounded.\n");
            return false;
        }
        
        // Find leaving variable (minimum ratio test)
        int leaving = -1;
        double min_ratio = INFINITY;
        
        for (int i = 0; i < m; i++) {
            if (p->A[i][entering] > 0) {
                double ratio = p->b[i] / p->A[i][entering];
                if (ratio < min_ratio) {
                    min_ratio = ratio;
                    leaving = i;
                }
            }
        }
        
        if (leaving == -1) {
            printf("Problem is unbounded.\n");
            return false;
        }
        
        // Pivot on A[leaving][entering]
        double pivot = p->A[leaving][entering];
        
        // Update leaving row
        for (int j = 0; j < total_vars; j++) {
            p->A[leaving][j] /= pivot;
        }
        p->b[leaving] /= pivot;
        
        // Update other rows
        for (int i = 0; i < m; i++) {
            if (i != leaving) {
                double factor = p->A[i][entering];
                for (int j = 0; j < total_vars; j++) {
                    p->A[i][j] -= factor * p->A[leaving][j];
                }
                p->b[i] -= factor * p->b[leaving];
            }
        }
        
        // Update objective
        double obj_factor = p->c[entering];
        for (int j = 0; j < total_vars; j++) {
            p->c[j] -= obj_factor * p->A[leaving][j];
        }
        
        // Update basis
        p->basis[leaving] = entering;
    }
    
    // Extract solution
    for (int i = 0; i < n; i++) {
        solution[i] = 0.0;
    }
    
    for (int i = 0; i < m; i++) {
        if (p->basis[i] < n) {
            solution[p->basis[i]] = p->b[i];
        }
    }
    
    return true;
}

int main() {
    // Example problem: Maximize 3x + 5y with constraints:
    // 2x + 4y ≤ 100
    // 3x + 2y ≤ 80
    // x + y ≤ 30
    LPProblem p;
    p.num_vars = 2;
    p.num_constraints = 3;
    
    // Initialize constraint matrix (add slack variables)
    double A[3][5] = {
        {2, 4, 1, 0, 0},  // 2x + 4y + s1 = 100
        {3, 2, 0, 1, 0},   // 3x + 2y + s2 = 80
        {1, 1, 0, 0, 1}    // x + y + s3 = 30
    };
    
    for (int i = 0; i < p.num_constraints; i++) {
        for (int j = 0; j < p.num_vars + p.num_constraints; j++) {
            p.A[i][j] = A[i][j];
        }
    }
    
    // Right-hand side
    p.b[0] = 100;
    p.b[1] = 80;
    p.b[2] = 30;
    
    // Objective function coefficients (maximize 3x + 5y)
    // Slack variables have 0 coefficient in objective
    p.c[0] = -3;  // x coefficient (negative because we're maximizing)
    p.c[1] = -5;  // y coefficient
    p.c[2] = 0;   // s1 coefficient
    p.c[3] = 0;   // s2 coefficient
    p.c[4] = 0;   // s3 coefficient
    
    double solution[2];
    bool success = simplex(&p, solution);
    
    if (success) {
        printf("Optimal solution found:\n");
        printf("x = %.2f\n", solution[0]);
        printf("y = %.2f\n", solution[1]);
        printf("Optimal value = %.2f\n", 
               -p.c[p.num_vars + p.num_constraints]); // Negate because we stored as negatives
    }
    
    return 0;
}
```

## Duality in Linear Programming

Every LP problem (primal) has a corresponding dual problem with important relationships:

**Primal (P):**
```
Maximize cᵀx
Subject to Ax ≤ b
           x ≥ 0
```

**Dual (D):**
```
Minimize bᵀy
Subject to Aᵀy ≥ c
           y ≥ 0
```

### Duality Theorems

1. **Weak Duality:** For any feasible x in P and y in D, cᵀx ≤ bᵀy
2. **Strong Duality:** If P has an optimal solution x*, then D has an optimal solution y* and cᵀx* = bᵀy*

## The Initial Basic Feasible Solution

Finding an initial BFS is crucial for starting the simplex algorithm. Methods include:

1. **Using Slack Variables:** When all constraints are "≤" and b ≥ 0, slack variables form an initial BFS
2. **Two-Phase Method:** For problems without obvious BFS
   - Phase I: Find a BFS by solving an auxiliary problem
   - Phase II: Solve the original problem starting from this BFS
3. **Big-M Method:** Combines Phases I and II by adding artificial variables with large penalties

### Example of Initial BFS

For the problem:
```
Maximize: 3x + 5y
Subject to:
  2x + 4y ≤ 100
  3x + 2y ≤ 80
  x + y ≤ 30
  x, y ≥ 0
```

Adding slack variables s₁, s₂, s₃:
```
2x + 4y + s₁ = 100
3x + 2y + s₂ = 80
x + y + s₃ = 30
```

The initial BFS is x=0, y=0, s₁=100, s₂=80, s₃=30, which is feasible since all variables are non-negative and satisfy the constraints.

## Conclusion

Linear programming provides a powerful framework for optimization problems with linear constraints. The simplex method remains one of the most important algorithms for solving LPs, though interior-point methods are also widely used for large-scale problems. Understanding standard and slack forms, duality, and the process of finding initial feasible solutions are key to mastering linear programming.
