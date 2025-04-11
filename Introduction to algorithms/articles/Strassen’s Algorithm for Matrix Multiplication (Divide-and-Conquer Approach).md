# **Strassen’s Algorithm for Matrix Multiplication (Divide-and-Conquer Approach)**

## **1. Introduction**
Matrix multiplication is a fundamental operation in computer science, used in graphics, machine learning, and scientific computing. The **naive matrix multiplication** algorithm runs in $\(O(n^3)\)$ time. **Strassen’s algorithm** reduces this to $\(O(n^{\log_2 7}) \approx O(n^{2.807})\)$ using a **divide-and-conquer** approach.

---

## **2. Standard Matrix Multiplication (Naive Approach)**
Given two $\(n \times n\)$ matrices \(A\) and \(B\), the product $\(C = A \times B\)$ is computed as:
$\[
C_{ij} = \sum_{k=1}^{n} A_{ik} \cdot B_{kj}
\]$
- **Time Complexity:** $\(O(n^3)\)$ (Three nested loops).

### **Pseudocode (Naive)**
```plaintext
for i from 1 to n:
    for j from 1 to n:
        C[i][j] = 0
        for k from 1 to n:
            C[i][j] += A[i][k] * B[k][j]
```

---

## **3. Strassen’s Algorithm (Divide-and-Conquer)**
### **Key Idea**
- **Divide** each matrix into four $\(\frac{n}{2} \times \frac{n}{2}\)$ submatrices.
- **Recursively compute** 7 products (instead of 8 in the naive method).
- **Combine** results to form the final matrix.

### **Steps**
1. **Partition** \(A\), \(B\), and \(C\) into submatrices:
```math
   A = \begin{bmatrix}
   A_{11} & A_{12} \\
   A_{21} & A_{22}
   \end{bmatrix}, \quad
   B = \begin{bmatrix}
   B_{11} & B_{12} \\
   B_{21} & B_{22}
   \end{bmatrix}, \quad
   C = \begin{bmatrix}
   C_{11} & C_{12} \\
   C_{21} & C_{22}
   \end{bmatrix}
```
2. **Compute 7 products**:
```math
   \begin{aligned}
   P_1 &= A_{11} \cdot (B_{12} - B_{22}) \\
   P_2 &= (A_{11} + A_{12}) \cdot B_{22} \\
   P_3 &= (A_{21} + A_{22}) \cdot B_{11} \\
   P_4 &= A_{22} \cdot (B_{21} - B_{11}) \\
   P_5 &= (A_{11} + A_{22}) \cdot (B_{11} + B_{22}) \\
   P_6 &= (A_{12} - A_{22}) \cdot (B_{21} + B_{22}) \\
   P_7 &= (A_{11} - A_{21}) \cdot (B_{11} + B_{12})
   \end{aligned}
```
3. **Combine results**:
```math
   \begin{aligned}
   C_{11} &= P_5 + P_4 - P_2 + P_6 \\
   C_{12} &= P_1 + P_2 \\
   C_{21} &= P_3 + P_4 \\
   C_{22} &= P_5 + P_1 - P_3 - P_7
   \end{aligned}
```

### **Time Complexity**
- **Recurrence Relation:** $\(T(n) = 7T(n/2) + O(n^2)\)$
- **Solution:** $\(T(n) = O(n^{\log_2 7}) \approx O(n^{2.807})\)$

---

## **4. Implementation in C**
```c
#include <stdio.h>
#include <stdlib.h>

// Function to add two matrices
void add(int n, int **A, int **B, int **C) {
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            C[i][j] = A[i][j] + B[i][j];
}

// Function to subtract two matrices
void subtract(int n, int **A, int **B, int **C) {
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            C[i][j] = A[i][j] - B[i][j];
}

// Function to multiply two matrices using Strassen's algorithm
void strassen(int n, int **A, int **B, int **C) {
    if (n == 1) {
        C[0][0] = A[0][0] * B[0][0];
        return;
    }

    int half = n / 2;

    // Allocate submatrices
    int **A11 = (int **)malloc(half * sizeof(int *));
    int **A12 = (int **)malloc(half * sizeof(int *));
    int **A21 = (int **)malloc(half * sizeof(int *));
    int **A22 = (int **)malloc(half * sizeof(int *));

    int **B11 = (int **)malloc(half * sizeof(int *));
    int **B12 = (int **)malloc(half * sizeof(int *));
    int **B21 = (int **)malloc(half * sizeof(int *));
    int **B22 = (int **)malloc(half * sizeof(int *));

    int **P1 = (int **)malloc(half * sizeof(int *));
    int **P2 = (int **)malloc(half * sizeof(int *));
    int **P3 = (int **)malloc(half * sizeof(int *));
    int **P4 = (int **)malloc(half * sizeof(int *));
    int **P5 = (int **)malloc(half * sizeof(int *));
    int **P6 = (int **)malloc(half * sizeof(int *));
    int **P7 = (int **)malloc(half * sizeof(int *));

    int **temp1 = (int **)malloc(half * sizeof(int *));
    int **temp2 = (int **)malloc(half * sizeof(int *));

    for (int i = 0; i < half; i++) {
        A11[i] = (int *)malloc(half * sizeof(int));
        A12[i] = (int *)malloc(half * sizeof(int));
        A21[i] = (int *)malloc(half * sizeof(int));
        A22[i] = (int *)malloc(half * sizeof(int));

        B11[i] = (int *)malloc(half * sizeof(int));
        B12[i] = (int *)malloc(half * sizeof(int));
        B21[i] = (int *)malloc(half * sizeof(int));
        B22[i] = (int *)malloc(half * sizeof(int));

        P1[i] = (int *)malloc(half * sizeof(int));
        P2[i] = (int *)malloc(half * sizeof(int));
        P3[i] = (int *)malloc(half * sizeof(int));
        P4[i] = (int *)malloc(half * sizeof(int));
        P5[i] = (int *)malloc(half * sizeof(int));
        P6[i] = (int *)malloc(half * sizeof(int));
        P7[i] = (int *)malloc(half * sizeof(int));

        temp1[i] = (int *)malloc(half * sizeof(int));
        temp2[i] = (int *)malloc(half * sizeof(int));
    }

    // Partition matrices into submatrices
    for (int i = 0; i < half; i++) {
        for (int j = 0; j < half; j++) {
            A11[i][j] = A[i][j];
            A12[i][j] = A[i][j + half];
            A21[i][j] = A[i + half][j];
            A22[i][j] = A[i + half][j + half];

            B11[i][j] = B[i][j];
            B12[i][j] = B[i][j + half];
            B21[i][j] = B[i + half][j];
            B22[i][j] = B[i + half][j + half];
        }
    }

    // Compute P1 to P7
    subtract(half, B12, B22, temp1);
    strassen(half, A11, temp1, P1);

    add(half, A11, A12, temp1);
    strassen(half, temp1, B22, P2);

    add(half, A21, A22, temp1);
    strassen(half, temp1, B11, P3);

    subtract(half, B21, B11, temp1);
    strassen(half, A22, temp1, P4);

    add(half, A11, A22, temp1);
    add(half, B11, B22, temp2);
    strassen(half, temp1, temp2, P5);

    subtract(half, A12, A22, temp1);
    add(half, B21, B22, temp2);
    strassen(half, temp1, temp2, P6);

    subtract(half, A11, A21, temp1);
    add(half, B11, B12, temp2);
    strassen(half, temp1, temp2, P7);

    // Calculate C submatrices
    add(half, P5, P4, temp1);
    subtract(half, temp1, P2, temp2);
    add(half, temp2, P6, C11);

    add(half, P1, P2, C12);

    add(half, P3, P4, C21);

    add(half, P5, P1, temp1);
    subtract(half, temp1, P3, temp2);
    subtract(half, temp2, P7, C22);

    // Combine C submatrices into C
    for (int i = 0; i < half; i++) {
        for (int j = 0; j < half; j++) {
            C[i][j] = C11[i][j];
            C[i][j + half] = C12[i][j];
            C[i + half][j] = C21[i][j];
            C[i + half][j + half] = C22[i][j];
        }
    }

    // Free allocated memory
    for (int i = 0; i < half; i++) {
        free(A11[i]); free(A12[i]); free(A21[i]); free(A22[i]);
        free(B11[i]); free(B12[i]); free(B21[i]); free(B22[i]);
        free(P1[i]); free(P2[i]); free(P3[i]); free(P4[i]);
        free(P5[i]); free(P6[i]); free(P7[i]);
        free(temp1[i]); free(temp2[i]);
    }
    free(A11); free(A12); free(A21); free(A22);
    free(B11); free(B12); free(B21); free(B22);
    free(P1); free(P2); free(P3); free(P4);
    free(P5); free(P6); free(P7);
    free(temp1); free(temp2);
}

int main() {
    int n = 4; // Matrix size (must be power of 2 for simplicity)
    int **A = (int **)malloc(n * sizeof(int *));
    int **B = (int **)malloc(n * sizeof(int *));
    int **C = (int **)malloc(n * sizeof(int *));

    for (int i = 0; i < n; i++) {
        A[i] = (int *)malloc(n * sizeof(int));
        B[i] = (int *)malloc(n * sizeof(int));
        C[i] = (int *)malloc(n * sizeof(int));
    }

    // Initialize matrices A and B (example)
    int a[4][4] = {{1, 2, 3, 4}, {5, 6, 7, 8}, {9, 10, 11, 12}, {13, 14, 15, 16}};
    int b[4][4] = {{16, 15, 14, 13}, {12, 11, 10, 9}, {8, 7, 6, 5}, {4, 3, 2, 1}};

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            A[i][j] = a[i][j];
            B[i][j] = b[i][j];
        }
    }

    // Compute C = A × B using Strassen's algorithm
    strassen(n, A, B, C);

    // Print result
    printf("Resultant Matrix C:\n");
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            printf("%d ", C[i][j]);
        }
        printf("\n");
    }

    // Free memory
    for (int i = 0; i < n; i++) {
        free(A[i]);
        free(B[i]);
        free(C[i]);
    }
    free(A);
    free(B);
    free(C);

    return 0;
}
```

---

## **5. Key Takeaways**
- **Strassen’s algorithm** reduces matrix multiplication time from $\(O(n^3)\)$ to $\(O(n^{2.807})\)$.
- **Recursive divide-and-conquer** approach with **7 multiplications** instead of 8.
- **Practical use**: Best for large matrices (though modern hardware favors optimized $\(O(n^3)\)$ methods due to hidden constants).

---

## **6. Limitations**
- **Only works for $\(n \times n\)$ matrices where $\(n\)$ is a power of 2** (padding can handle other sizes).
- **Higher memory usage** due to recursion.
- **Not always faster** for small matrices due to overhead.

---


