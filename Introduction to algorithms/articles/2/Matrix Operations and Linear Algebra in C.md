# Matrix Operations and Linear Algebra in C

This comprehensive guide covers matrix operations, solving linear systems, matrix inversion, and applications to symmetric positive-definite matrices and least-squares approximation. I'll explain each concept in detail and provide C implementations.

## Matrix Operations

### Basic Matrix Operations

Matrix operations include addition, subtraction, multiplication, and scalar operations.

**Matrix Addition**:
For two matrices A and B of the same size m×n:
$\[
C = A + B \quad \text{where} \quad c_{ij} = a_{ij} + b_{ij}
\]$

**Matrix Multiplication**:
For A (m×n) and B (n×p):
$\[
C = AB \quad \text{where} \quad c_{ij} = \sum_{k=1}^n a_{ik}b_{kj}
\]$

### Transpose

The transpose of a matrix A (m×n) is AT (n×m) where:
\[(AT)_{ij} = A_{ji}\]

## Solving Systems of Linear Equations

A system of linear equations can be represented in matrix form:
$\[
Ax = b
\]$
where A is an n×n matrix, x is the unknown vector, and b is the right-hand side vector.

### Gaussian Elimination

The standard method for solving linear systems:

1. Form augmented matrix [A|b]
2. Perform row operations to get upper triangular form
3. Back substitution to solve for x

**Example**:
For the system:

```math
\begin{cases}
2x + y - z = 8 \\
-3x - y + 2z = -11 \\
-2x + y + 2z = -3
\end{cases}
```

The matrix form is:
$$\[
\begin{pmatrix}
2 & 1 & -1 \\
-3 & -1 & 2 \\
-2 & 1 & 2
\end{pmatrix}
\begin{pmatrix}
x \\
y \\
z
\end{pmatrix}
=
\begin{pmatrix}
8 \\
-11 \\
-3
\end{pmatrix}
\]$$

## Inverting Matrices

The inverse of a square matrix A is a matrix A⁻¹ such that:
$\[
AA⁻¹ = A⁻¹A = I
\]$

### Methods for Matrix Inversion

1. **Gauss-Jordan Elimination**:
   - Augment A with I
   - Perform row operations to reduce A to I
   - The augmented part becomes A⁻¹

2. **Adjugate Method**:
   $\[
   A⁻¹ = \frac{1}{\det(A)} \text{adj}(A)
   \]$
   where adj(A) is the adjugate matrix.

## Symmetric Positive-Definite Matrices

A matrix A is symmetric positive-definite if:
1. A = AT (symmetric)
2. xTAx > 0 for all non-zero x

### Properties
- All eigenvalues are positive
- All leading principal minors have positive determinants
- Cholesky decomposition exists: A = LLT

## Least-Squares Approximation

For overdetermined systems (more equations than variables) Ax ≈ b, the least-squares solution minimizes:
$\[
\|Ax - b\|^2
\]$

The normal equations give the solution:
$\[
A^TAx = A^Tb \quad \Rightarrow \quad x = (A^TA)^{-1}A^Tb
\]$


## Complete C Implementation

Here's a comprehensive C implementation covering all these operations:

```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>

#define MAX_SIZE 10
#define TOLERANCE 1e-10

typedef struct {
    int rows;
    int cols;
    double data[MAX_SIZE][MAX_SIZE];
} Matrix;

// Function prototypes
Matrix create_matrix(int rows, int cols);
void print_matrix(Matrix m);
Matrix matrix_add(Matrix a, Matrix b);
Matrix matrix_multiply(Matrix a, Matrix b);
Matrix matrix_transpose(Matrix m);
int gaussian_elimination(Matrix a, Matrix *b, double *x);
Matrix matrix_inverse(Matrix m);
int is_spd(Matrix m);
Matrix cholesky_decomposition(Matrix m);
Matrix least_squares(Matrix a, Matrix b);

int main() {
    // Example usage
    Matrix A = create_matrix(3, 3);
    A.data[0][0] = 2; A.data[0][1] = 1; A.data[0][2] = -1;
    A.data[1][0] = -3; A.data[1][1] = -1; A.data[1][2] = 2;
    A.data[2][0] = -2; A.data[2][1] = 1; A.data[2][2] = 2;
    
    Matrix b = create_matrix(3, 1);
    b.data[0][0] = 8;
    b.data[1][0] = -11;
    b.data[2][0] = -3;
    
    printf("Solving Ax=b:\n");
    double x[MAX_SIZE];
    if (gaussian_elimination(A, &b, x)) {
        printf("Solution: x = [");
        for (int i = 0; i < A.rows; i++) {
            printf("%.4f%s", x[i], (i < A.rows-1) ? ", " : "]\n");
        }
    }
    
    printf("\nMatrix inverse:\n");
    Matrix invA = matrix_inverse(A);
    print_matrix(invA);
    
    printf("\nChecking if symmetric positive-definite:\n");
    Matrix B = create_matrix(2, 2);
    B.data[0][0] = 4; B.data[0][1] = 1;
    B.data[1][0] = 1; B.data[1][1] = 3;
    
    if (is_spd(B)) {
        printf("Matrix is SPD\n");
        printf("Cholesky decomposition:\n");
        Matrix L = cholesky_decomposition(B);
        print_matrix(L);
    } else {
        printf("Matrix is not SPD\n");
    }
    
    printf("\nLeast squares approximation:\n");
    Matrix C = create_matrix(4, 2);
    C.data[0][0] = 1; C.data[0][1] = 1;
    C.data[1][0] = 1; C.data[1][1] = 2;
    C.data[2][0] = 1; C.data[2][1] = 3;
    C.data[3][0] = 1; C.data[3][1] = 4;
    
    Matrix d = create_matrix(4, 1);
    d.data[0][0] = 6;
    d.data[1][0] = 5;
    d.data[2][0] = 7;
    d.data[3][0] = 10;
    
    Matrix ls_solution = least_squares(C, d);
    printf("Least squares solution:\n");
    print_matrix(ls_solution);
    
    return 0;
}

Matrix create_matrix(int rows, int cols) {
    Matrix m;
    m.rows = rows;
    m.cols = cols;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            m.data[i][j] = 0;
        }
    }
    return m;
}

void print_matrix(Matrix m) {
    for (int i = 0; i < m.rows; i++) {
        for (int j = 0; j < m.cols; j++) {
            printf("%8.4f ", m.data[i][j]);
        }
        printf("\n");
    }
}

Matrix matrix_add(Matrix a, Matrix b) {
    Matrix result = create_matrix(a.rows, a.cols);
    for (int i = 0; i < a.rows; i++) {
        for (int j = 0; j < a.cols; j++) {
            result.data[i][j] = a.data[i][j] + b.data[i][j];
        }
    }
    return result;
}

Matrix matrix_multiply(Matrix a, Matrix b) {
    Matrix result = create_matrix(a.rows, b.cols);
    for (int i = 0; i < a.rows; i++) {
        for (int j = 0; j < b.cols; j++) {
            result.data[i][j] = 0;
            for (int k = 0; k < a.cols; k++) {
                result.data[i][j] += a.data[i][k] * b.data[k][j];
            }
        }
    }
    return result;
}

Matrix matrix_transpose(Matrix m) {
    Matrix result = create_matrix(m.cols, m.rows);
    for (int i = 0; i < m.rows; i++) {
        for (int j = 0; j < m.cols; j++) {
            result.data[j][i] = m.data[i][j];
        }
    }
    return result;
}

int gaussian_elimination(Matrix a, Matrix *b, double *x) {
    int n = a.rows;
    
    // Forward elimination
    for (int k = 0; k < n; k++) {
        // Find pivot row
        int max_row = k;
        for (int i = k + 1; i < n; i++) {
            if (fabs(a.data[i][k]) > fabs(a.data[max_row][k])) {
                max_row = i;
            }
        }
        
        // Swap rows if necessary
        if (max_row != k) {
            for (int j = k; j < n; j++) {
                double temp = a.data[k][j];
                a.data[k][j] = a.data[max_row][j];
                a.data[max_row][j] = temp;
            }
            for (int j = 0; j < b->cols; j++) {
                double temp = b->data[k][j];
                b->data[k][j] = b->data[max_row][j];
                b->data[max_row][j] = temp;
            }
        }
        
        // Check for singular matrix
        if (fabs(a.data[k][k]) < TOLERANCE) {
            return 0; // Singular matrix
        }
        
        // Eliminate
        for (int i = k + 1; i < n; i++) {
            double factor = a.data[i][k] / a.data[k][k];
            for (int j = k; j < n; j++) {
                a.data[i][j] -= factor * a.data[k][j];
            }
            for (int j = 0; j < b->cols; j++) {
                b->data[i][j] -= factor * b->data[k][j];
            }
        }
    }
    
    // Back substitution
    for (int k = n - 1; k >= 0; k--) {
        for (int j = 0; j < b->cols; j++) {
            x[k] = b->data[k][j];
            for (int i = k + 1; i < n; i++) {
                x[k] -= a.data[k][i] * x[i];
            }
            x[k] /= a.data[k][k];
        }
    }
    
    return 1;
}

Matrix matrix_inverse(Matrix m) {
    Matrix inverse = create_matrix(m.rows, m.cols);
    Matrix identity = create_matrix(m.rows, m.cols);
    
    // Create identity matrix
    for (int i = 0; i < m.rows; i++) {
        identity.data[i][i] = 1;
    }
    
    // Perform Gauss-Jordan elimination
    for (int i = 0; i < m.rows; i++) {
        // Find pivot row
        int max_row = i;
        for (int k = i + 1; k < m.rows; k++) {
            if (fabs(m.data[k][i]) > fabs(m.data[max_row][i])) {
                max_row = k;
            }
        }
        
        // Swap rows if necessary
        if (max_row != i) {
            for (int j = 0; j < m.cols; j++) {
                double temp = m.data[i][j];
                m.data[i][j] = m.data[max_row][j];
                m.data[max_row][j] = temp;
                
                temp = identity.data[i][j];
                identity.data[i][j] = identity.data[max_row][j];
                identity.data[max_row][j] = temp;
            }
        }
        
        // Check for singular matrix
        if (fabs(m.data[i][i]) < TOLERANCE) {
            printf("Matrix is singular, cannot invert.\n");
            return inverse; // Return zero matrix
        }
        
        // Normalize the pivot row
        double pivot = m.data[i][i];
        for (int j = 0; j < m.cols; j++) {
            m.data[i][j] /= pivot;
            identity.data[i][j] /= pivot;
        }
        
        // Eliminate other rows
        for (int k = 0; k < m.rows; k++) {
            if (k != i && fabs(m.data[k][i]) > TOLERANCE) {
                double factor = m.data[k][i];
                for (int j = 0; j < m.cols; j++) {
                    m.data[k][j] -= factor * m.data[i][j];
                    identity.data[k][j] -= factor * identity.data[i][j];
                }
            }
        }
    }
    
    // The inverse is now in the identity matrix
    return identity;
}

int is_spd(Matrix m) {
    // Check if symmetric
    for (int i = 0; i < m.rows; i++) {
        for (int j = 0; j < i; j++) {
            if (fabs(m.data[i][j] - m.data[j][i]) > TOLERANCE) {
                return 0; // Not symmetric
            }
        }
    }
    
    // Check if positive definite using leading principal minors
    for (int k = 1; k <= m.rows; k++) {
        Matrix sub = create_matrix(k, k);
        for (int i = 0; i < k; i++) {
            for (int j = 0; j < k; j++) {
                sub.data[i][j] = m.data[i][j];
            }
        }
        
        // Perform LU decomposition to get determinant
        double det = 1.0;
        for (int i = 0; i < k; i++) {
            det *= sub.data[i][i];
            if (det <= 0) return 0;
            
            for (int j = i + 1; j < k; j++) {
                double factor = sub.data[j][i] / sub.data[i][i];
                for (int l = i + 1; l < k; l++) {
                    sub.data[j][l] -= factor * sub.data[i][l];
                }
            }
        }
    }
    
    return 1;
}

Matrix cholesky_decomposition(Matrix m) {
    Matrix L = create_matrix(m.rows, m.cols);
    
    for (int i = 0; i < m.rows; i++) {
        for (int j = 0; j <= i; j++) {
            double sum = 0;
            
            if (j == i) { // Diagonal elements
                for (int k = 0; k < j; k++) {
                    sum += L.data[j][k] * L.data[j][k];
                }
                L.data[j][j] = sqrt(m.data[j][j] - sum);
            } else { // Off-diagonal elements
                for (int k = 0; k < j; k++) {
                    sum += L.data[i][k] * L.data[j][k];
                }
                L.data[i][j] = (m.data[i][j] - sum) / L.data[j][j];
            }
        }
    }
    
    return L;
}

Matrix least_squares(Matrix a, Matrix b) {
    Matrix aT = matrix_transpose(a);
    Matrix aTa = matrix_multiply(aT, a);
    Matrix aTb = matrix_multiply(aT, b);
    
    // Solve aTa * x = aTb
    double x[MAX_SIZE];
    if (gaussian_elimination(aTa, &aTb, x)) {
        Matrix result = create_matrix(a.cols, 1);
        for (int i = 0; i < a.cols; i++) {
            result.data[i][0] = x[i];
        }
        return result;
    } else {
        printf("Least squares failed.\n");
        return create_matrix(a.cols, 1);
    }
}
```

### Key Features of the Implementation:

1. **Matrix Structure**: Uses a simple struct to represent matrices with fixed maximum size
2. **Basic Operations**: Includes addition, multiplication, and transpose
3. **Linear System Solver**: Implements Gaussian elimination with partial pivoting
4. **Matrix Inversion**: Uses Gauss-Jordan elimination
5. **SPD Checking**: Verifies symmetry and positive definiteness
6. **Cholesky Decomposition**: For SPD matrices
7. **Least Squares**: Solves normal equations using the above methods
