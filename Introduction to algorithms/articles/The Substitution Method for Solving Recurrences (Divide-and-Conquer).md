# **The Substitution Method for Solving Recurrences (Divide-and-Conquer)**

## **1. Introduction**
The **substitution method** is a technique to solve recurrence relations, commonly arising in **divide-and-conquer algorithms** (e.g., Merge Sort, Strassen’s Matrix Multiplication). It involves:
1. **Guessing** the form of the solution.
2. **Proving** it correct using **mathematical induction**.

---

## **2. Steps in the Substitution Method**
### **(1) Guess the Solution**
- Based on recursion pattern, hypothesize a closed-form solution (e.g., $\(T(n) = O(n \log n)\)$ for Merge Sort).

### **(2) Verify Using Induction**
- **Base Case:** Check if the guess holds for small \(n\).
- **Inductive Step:** Assume it holds for $\(n/2\)$ (or smaller inputs), then prove for \(n\).

### **(3) Solve for Constants**
- Adjust constants to satisfy the recurrence.

---

## **3. Example: Solving Merge Sort’s Recurrence**
### **Recurrence Relation**
$\[
T(n) = 2T\left(\frac{n}{2}\right) + O(n)
\]$
**Guess:** $\(T(n) = O(n \log n)\)$.

### **Inductive Proof**
1. **Assume:** $\(T(k) \leq c k \log k\)$ for all \(k < n\).
2. **Show:** $\(T(n) \leq c n \log n\)$.
   $\[
   T(n) \leq 2 \left( c \cdot \frac{n}{2} \log \left(\frac{n}{2}\right) \right) + n
   \]$
   
   $\[
   = c n (\log n - 1) + n
   \]$
   
   $\[
   = c n \log n - c n + n
   \]$
   
   $\[
   \leq c n \log n \quad \text{(if } c \geq 1 \text{)}
   \]$

---

## **4. General Approach for Divide-and-Conquer Recurrences**
### **Recurrence Form**
$\[
T(n) = a T\left(\frac{n}{b}\right) + f(n)
\]$
- \(a\): Number of subproblems.
- \(b\): Factor by which input size shrinks.
- \(f(n)\): Cost of dividing/combining.

### **Common Cases**
| Case          | Condition               | Solution               | Example Algorithms      |
|---------------|-------------------------|------------------------|-------------------------|
| **Case 1**    | $\(f(n) = O(n^{c-\epsilon})\)$ | $\(T(n) = \Theta(n^c)\)$ | Binary Search (\(c=0\)) |
| **Case 2**    | $\(f(n) = \Theta(n^c \log^k n)\)$ | $\(T(n) = \Theta(n^c \log^{k+1} n)\)$ | Merge Sort $(\(c=1, k=0\))$ |
| **Case 3**    | $\(f(n) = \Omega(n^{c+\epsilon})\)$ | $\(T(n) = \Theta(f(n))\)$ | Strassen’s Algorithm |

---

## **5. Implementation in C (Merge Sort Example)**
```c
#include <stdio.h>
#include <stdlib.h>

// Merge two sorted subarrays
void merge(int arr[], int l, int m, int r) {
    int n1 = m - l + 1;
    int n2 = r - m;

    int *L = (int *)malloc(n1 * sizeof(int));
    int *R = (int *)malloc(n2 * sizeof(int));

    for (int i = 0; i < n1; i++) L[i] = arr[l + i];
    for (int j = 0; j < n2; j++) R[j] = arr[m + 1 + j];

    int i = 0, j = 0, k = l;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) arr[k++] = L[i++];
        else arr[k++] = R[j++];
    }

    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];

    free(L); free(R);
}

// Merge Sort (Divide-and-Conquer)
void mergeSort(int arr[], int l, int r) {
    if (l < r) {
        int m = l + (r - l) / 2;
        mergeSort(arr, l, m);      // Recur on left half
        mergeSort(arr, m + 1, r);  // Recur on right half
        merge(arr, l, m, r);       // Combine
    }
}

int main() {
    int arr[] = {12, 11, 13, 5, 6, 7};
    int n = sizeof(arr) / sizeof(arr[0]);

    mergeSort(arr, 0, n - 1);

    printf("Sorted array: ");
    for (int i = 0; i < n; i++) printf("%d ", arr[i]);

    return 0;
}
```

---

## **6. Solving Recurrences: Strassen’s Algorithm Example**
### **Recurrence**
$\[
T(n) = 7T\left(\frac{n}{2}\right) + O(n^2)
\]$
**Guess:** $\(T(n) = O(n^{\log_2 7}) \approx O(n^{2.807})\)$.

### **Inductive Proof**
1. **Assume:** $\(T(k) \leq c k^{\log_2 7}\) for \(k < n\)$.
2. **Show:** $\(T(n) \leq c n^{\log_2 7}\)$.
   $\[
   T(n) \leq 7 \left( c \left(\frac{n}{2}\right)^{\log_2 7} \right) + n^2
   \]$
   
   $\[
   = \frac{7c}{7} n^{\log_2 7} + n^2 = c n^{\log_2 7} + n^2
   \]$
   
   $\[
   \leq c n^{\log_2 7} \quad \text{(for large } c \text{)}
   \]$

---

## **7. Key Takeaways**
- **Substitution method** combines guessing and induction.
- **Master Theorem** provides a shortcut for common recurrences.
- **Merge Sort** and **Strassen’s Algorithm** are classic examples.

---

## **8. When to Use Substitution?**
- **Master Theorem doesn’t apply** $(e.g., \(T(n) = T(n-1) + n\))$.
- **Exact solutions** are needed (not just asymptotic bounds).

---

## **9. Limitations**
- **Guessing correctly** requires practice.
- **Inductive proofs** can be tedious for complex recurrences.

---
