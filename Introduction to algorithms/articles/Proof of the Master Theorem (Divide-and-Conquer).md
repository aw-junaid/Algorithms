# **Proof of the Master Theorem (Divide-and-Conquer)**

The **Master Theorem** provides asymptotic bounds for recurrences of the form:
$\[
T(n) = aT\left(\frac{n}{b}\right) + f(n)
\]$
where:
- $\(a \geq 1\)$ (number of subproblems),
- $\(b > 1\)$ (factor by which the problem size shrinks),
- $\(f(n)\)$ is the cost of dividing/combining.

This section explains the **proof of the Master Theorem** and implements an example in C.

---

## **1. Overview of the Master Theorem**
The Master Theorem has **three cases** based on the relationship between $\(f(n)\)$ and $\(n^{\log_b a}\)$:

| **Case**  | **Condition** | **Solution** |
|-----------|--------------|--------------|
| **Case 1** | $\(f(n) = O(n^{\log_b a - \epsilon})\)$ | $\(T(n) = \Theta(n^{\log_b a})\)$ |
| **Case 2** | $\(f(n) = \Theta(n^{\log_b a} \log^k n)\)$ | $\(T(n) = \Theta(n^{\log_b a} \log^{k+1} n)\)$ |
| **Case 3** | $\(f(n) = \Omega(n^{\log_b a + \epsilon})\)$ and $\(af(n/b) \leq cf(n)\)$ | $\(T(n) = \Theta(f(n))\)$ |

---

## **2. Intuition Behind the Proof**
The proof relies on analyzing the **recursion tree** of $\(T(n)\)$:
1. **Total levels** = $\(\log_b n\)$ (since problem size reduces by \(b\) each time).
2. **Number of leaves** = $\(a^{\log_b n} = n^{\log_b a}\)$.
3. **Total work** is the sum of:
   - **Work done at each level** (combining subproblems).
   - **Work done at the leaves**.

### **Key Insight**
- **Case 1:** Most work is done at the **leaves** $(\(n^{\log_b a}\)$ dominates).
- **Case 2:** Work is **evenly distributed** across levels.
- **Case 3:** Most work is done at the **root** $(\(f(n)\)$ dominates).

---

## **3. Detailed Proof**
### **Step 1: Recursion Tree Expansion**
$\[
T(n) = f(n) + aT\left(\frac{n}{b}\right)
\]$
Expanding recursively:
$\[
T(n) = f(n) + a f\left(\frac{n}{b}\right) + a^2 f\left(\frac{n}{b^2}\right) + \dots + a^{\log_b n} f(1)
\]$
where:
- The last term $\(a^{\log_b n} f(1) = \Theta(n^{\log_b a})\) (since \(f(1) = \Theta(1)\))$.

### **Step 2: Summing the Work**
The total work is:
$\[
T(n) = \sum_{i=0}^{\log_b n} a^i f\left(\frac{n}{b^i}\right)
\]$

#### **Case 1: $\(f(n) = O(n^{\log_b a - \epsilon})\)$**
- The sum is dominated by the **leaves** $(\(n^{\log_b a}\))$.
- Geometric series converges to $\(\Theta(n^{\log_b a})\)$.

#### **Case 2: $\(f(n) = \Theta(n^{\log_b a} \log^k n)\)$**
- Each level contributes $\(\Theta(n^{\log_b a} \log^k n)\)$.
- Total work = $\(\Theta(n^{\log_b a} \log^{k+1} n)\)$.

#### **Case 3: $\(f(n) = \Omega(n^{\log_b a + \epsilon})\)$**
- The sum is dominated by the **root** $(\(f(n)\))$.
- The **regularity condition** $(\(af(n/b) \leq cf(n)\))$ ensures the series converges.

---

## **4. Implementation in C (Binary Search Example)**
Binary Search follows the recurrence:
$\[
T(n) = T\left(\frac{n}{2}\right) + O(1)
\]$
- **Master Theorem Parameters**:
  - $\(a = 1\), \(b = 2\), \(f(n) = 1\)$.
  - $\(n^{\log_b a} = n^{\log_2 1} = 1\)$.
  - **Case 2:** $\(f(n) = \Theta(1) = \Theta(n^{\log_b a} \log^0 n)\)$.
  - **Solution:** $\(T(n) = \Theta(\log n)\)$.

### **C Code**
```c
#include <stdio.h>

// Binary Search (Divide-and-Conquer)
int binarySearch(int arr[], int l, int r, int x) {
    if (r >= l) {
        int mid = l + (r - l) / 2;
        if (arr[mid] == x) return mid;
        if (arr[mid] > x) return binarySearch(arr, l, mid - 1, x);
        return binarySearch(arr, mid + 1, r, x);
    }
    return -1;
}

int main() {
    int arr[] = {2, 3, 4, 10, 40};
    int n = sizeof(arr) / sizeof(arr[0]);
    int x = 10;
    int result = binarySearch(arr, 0, n - 1, x);
    printf("Element found at index: %d\n", result);
    return 0;
}
```

---

## **5. Example: Merge Sort**
### **Recurrence**
$\[
T(n) = 2T\left(\frac{n}{2}\right) + O(n)
\]$
- **Master Theorem Parameters**:
  - $\(a = 2\)$, $\(b = 2\), \(f(n) = n\)$.
  - $\(n^{\log_b a} = n^{\log_2 2} = n\)$.
  - **Case 2:** $\(f(n) = \Theta(n) = \Theta(n^{\log_b a} \log^0 n)\)$.
  - **Solution:** $\(T(n) = \Theta(n \log n)\)$.

### **C Code**
```c
#include <stdio.h>
#include <stdlib.h>

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

void mergeSort(int arr[], int l, int r) {
    if (l < r) {
        int m = l + (r - l) / 2;
        mergeSort(arr, l, m);
        mergeSort(arr, m + 1, r);
        merge(arr, l, m, r);
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

## **6. Key Takeaways**
1. **Master Theorem** provides asymptotic bounds for divide-and-conquer recurrences.
2. **Three cases** based on $\(f(n)\)$ vs. $\(n^{\log_b a}\)$.
3. **Binary Search** = $\(\Theta(\log n)\)$, **Merge Sort** = $\(\Theta(n \log n)\)$.

---

## **7. Limitations**
- **Does not apply** to all recurrences $(e.g., \(T(n) = T(n-1) + 1\))$.
- **Regularity condition** must hold for Case 3.

---
