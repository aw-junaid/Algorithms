# Matrices: A Comprehensive Guide

## Introduction to Matrices

A matrix is a rectangular array of numbers, symbols, or expressions arranged in rows and columns. Matrices are fundamental mathematical objects that are widely used in various fields including physics, computer graphics, statistics, and machine learning.

## Basic Matrix Notation

A matrix is typically denoted by a capital letter (e.g., A) and its elements by lowercase letters with subscripts indicating their position:

```
A = [a₁₁ a₁₂ ... a₁ₙ
     a₂₁ a₂₂ ... a₂ₙ
     ... ... ... ...
     aₘ₁ aₘ₂ ... aₘₙ]
```

Where:
- m = number of rows
- n = number of columns
- aᵢⱼ = element at row i, column j

## Types of Matrices

### 1. Row Matrix
A matrix with only one row: `[a₁₁ a₁₂ ... a₁ₙ]`

### 2. Column Matrix
A matrix with only one column: `[a₁₁; a₂₁; ...; aₘ₁]`

### 3. Square Matrix
A matrix with equal number of rows and columns (m = n)

### 4. Diagonal Matrix
A square matrix where all elements outside the main diagonal are zero

### 5. Identity Matrix
A diagonal matrix with all diagonal elements equal to 1 (denoted by I)

### 6. Zero Matrix
A matrix where all elements are zero (denoted by 0)

### 7. Symmetric Matrix
A square matrix that is equal to its transpose (A = Aᵀ)

### 8. Triangular Matrix
- Upper triangular: All elements below the diagonal are zero
- Lower triangular: All elements above the diagonal are zero

## Matrix Operations

### 1. Matrix Addition
Two matrices of the same dimensions can be added by adding corresponding elements.

**Algorithm in C:**
```c
void matrix_add(int m, int n, float A[m][n], float B[m][n], float result[m][n]) {
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            result[i][j] = A[i][j] + B[i][j];
        }
    }
}
```

### 2. Matrix Subtraction
Similar to addition, but subtract corresponding elements.

**Algorithm in C:**
```c
void matrix_subtract(int m, int n, float A[m][n], float B[m][n], float result[m][n]) {
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            result[i][j] = A[i][j] - B[i][j];
        }
    }
}
```

### 3. Scalar Multiplication
Multiply every element of a matrix by a scalar value.

**Algorithm in C:**
```c
void scalar_multiply(int m, int n, float scalar, float A[m][n], float result[m][n]) {
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            result[i][j] = scalar * A[i][j];
        }
    }
}
```

### 4. Matrix Multiplication
The product of two matrices A (m×n) and B (n×p) is a matrix C (m×p) where:

```
Cᵢⱼ = Σ (Aᵢₖ * Bₖⱼ) for k = 1 to n
```

**Algorithm in C:**
```c
void matrix_multiply(int m, int n, int p, float A[m][n], float B[n][p], float result[m][p]) {
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < p; j++) {
            result[i][j] = 0;
            for (int k = 0; k < n; k++) {
                result[i][j] += A[i][k] * B[k][j];
            }
        }
    }
}
```

### 5. Matrix Transpose
The transpose of a matrix A (m×n) is a matrix Aᵀ (n×m) where Aᵀᵢⱼ = Aⱼᵢ.

**Algorithm in C:**
```c
void matrix_transpose(int m, int n, float A[m][n], float result[n][m]) {
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            result[j][i] = A[i][j];
        }
    }
}
```

## Matrix Properties

### 1. Associative Property
- Matrix addition: (A + B) + C = A + (B + C)
- Matrix multiplication: (AB)C = A(BC)

### 2. Commutative Property (Addition only)
A + B = B + A

### 3. Distributive Property
A(B + C) = AB + AC

### 4. Multiplicative Identity
AI = IA = A (where I is the identity matrix)

### 5. Transpose Properties
- (Aᵀ)ᵀ = A
- (A + B)ᵀ = Aᵀ + Bᵀ
- (AB)ᵀ = BᵀAᵀ

## Special Matrix Operations

### 1. Trace of a Matrix (Square matrices only)
Sum of the diagonal elements: tr(A) = Σ aᵢᵢ

**Algorithm in C:**
```c
float matrix_trace(int n, float A[n][n]) {
    float trace = 0;
    for (int i = 0; i < n; i++) {
        trace += A[i][i];
    }
    return trace;
}
```

### 2. Determinant (Square matrices only)
A scalar value that can be computed from the elements of a square matrix.

For a 2×2 matrix:
```
det(A) = a₁₁a₂₂ - a₁₂a₂₁
```

**Algorithm in C (for 2×2 matrix):**
```c
float matrix_determinant_2x2(float A[2][2]) {
    return A[0][0] * A[1][1] - A[0][1] * A[1][0];
}
```

### 3. Matrix Inverse (Square matrices only)
A matrix A⁻¹ such that AA⁻¹ = A⁻¹A = I (identity matrix)

For a 2×2 matrix:
```
A⁻¹ = (1/det(A)) * [a₂₂ -a₁₂; -a₂₁ a₁₁]
```

**Algorithm in C (for 2×2 matrix):**
```c
int matrix_inverse_2x2(float A[2][2], float result[2][2]) {
    float det = matrix_determinant_2x2(A);
    if (det == 0) return 0; // Matrix is not invertible
    
    float inv_det = 1.0 / det;
    result[0][0] =  A[1][1] * inv_det;
    result[0][1] = -A[0][1] * inv_det;
    result[1][0] = -A[1][0] * inv_det;
    result[1][1] =  A[0][0] * inv_det;
    
    return 1; // Success
}
```

## Matrix Representation in C

Here's a complete C program demonstrating matrix operations:

```c
#include <stdio.h>
#include <stdlib.h>

// Function prototypes
void print_matrix(int m, int n, float matrix[m][n]);
void matrix_add(int m, int n, float A[m][n], float B[m][n], float result[m][n]);
void matrix_multiply(int m, int n, int p, float A[m][n], float B[n][p], float result[m][p]);
void matrix_transpose(int m, int n, float A[m][n], float result[n][m]);

int main() {
    // Example matrices
    float A[2][3] = {{1, 2, 3}, {4, 5, 6}};
    float B[2][3] = {{6, 5, 4}, {3, 2, 1}};
    float C[3][2] = {{1, 2}, {3, 4}, {5, 6}};
    
    // Result matrices
    float sum[2][3];
    float product[2][2];
    float transpose[3][2];
    
    printf("Matrix A:\n");
    print_matrix(2, 3, A);
    
    printf("\nMatrix B:\n");
    print_matrix(2, 3, B);
    
    // Matrix addition
    matrix_add(2, 3, A, B, sum);
    printf("\nA + B:\n");
    print_matrix(2, 3, sum);
    
    printf("\nMatrix C:\n");
    print_matrix(3, 2, C);
    
    // Matrix multiplication
    matrix_multiply(2, 3, 2, A, C, product);
    printf("\nA × C:\n");
    print_matrix(2, 2, product);
    
    // Matrix transpose
    matrix_transpose(2, 3, A, transpose);
    printf("\nTranspose of A:\n");
    print_matrix(3, 2, transpose);
    
    return 0;
}

// Function to print a matrix
void print_matrix(int m, int n, float matrix[m][n]) {
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            printf("%6.2f ", matrix[i][j]);
        }
        printf("\n");
    }
}

// Function to add two matrices
void matrix_add(int m, int n, float A[m][n], float B[m][n], float result[m][n]) {
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            result[i][j] = A[i][j] + B[i][j];
        }
    }
}

// Function to multiply two matrices
void matrix_multiply(int m, int n, int p, float A[m][n], float B[n][p], float result[m][p]) {
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < p; j++) {
            result[i][j] = 0;
            for (int k = 0; k < n; k++) {
                result[i][j] += A[i][k] * B[k][j];
            }
        }
    }
}

// Function to compute matrix transpose
void matrix_transpose(int m, int n, float A[m][n], float result[n][m]) {
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            result[j][i] = A[i][j];
        }
    }
}
```

---
