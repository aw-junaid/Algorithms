# **The Master Method for Solving Recurrences (Divide-and-Conquer)**

## **1. Introduction to the Master Method**
The **Master Method** provides a **"cookbook" solution** for recurrence relations of the form:
$\[
T(n) = aT\left(\frac{n}{b}\right) + f(n)
\]$
where:
- $\(a \geq 1\)$ (number of subproblems),
- $\(b > 1\)$ (factor by which the problem size shrinks),
- $\(f(n)\)$ is the cost of dividing/combining.

### **Why Use the Master Method?**
- Faster than **substitution** or **recursion-tree** methods.
- Directly gives **asymptotic bounds** $(\(O, \Theta, \Omega\))$.

---

## **2. Master Theorem (Standard Form)**
Given $\(T(n) = aT(n/b) + f(n)\)$, compare $\(f(n)\) with \(n^{\log_b a}\)$:

### **Case 1: $\(f(n) = O(n^{\log_b a - \epsilon})\)$ for some $\(\epsilon > 0\)$**
- **Solution:** $\(T(n) = \Theta(n^{\log_b a})\)$  
- **Interpretation:** Leaf nodes dominate.  
- **Example:** Binary Search $(\(a=1, b=2, f(n)=1\))$  
  $\[
  T(n) = \Theta(\log n)
  \]$

### **Case 2: $\(f(n) = \Theta(n^{\log_b a} \log^k n)\)$**  
- **Solution:** $\(T(n) = \Theta(n^{\log_b a} \log^{k+1} n)\)$  
- **Interpretation:** Work is evenly distributed across levels.  
- **Example:** Merge Sort $(\(a=2, b=2, f(n)=n\))$  
  $\[
  T(n) = \Theta(n \log n)
  \]$

### **Case 3: $\(f(n) = \Omega(n^{\log_b a + \epsilon})\) and \(af(n/b) \leq cf(n)\) for some \(c < 1\)$**  
- **Solution:** $\(T(n) = \Theta(f(n))\)$  
- **Interpretation:** Root node dominates.  
- **Example:** Strassen’s Algorithm $(\(a=7, b=2, f(n)=n^2\))$  
  $\[
  T(n) = \Theta(n^{\log_2 7}) \approx \Theta(n^{2.807})
  \]$

---

## **3. Examples with Master Theorem**

### **Example 1: Merge Sort**
- **Recurrence:** $\(T(n) = 2T(n/2) + n\)$  
- **Parameters:**  
  $\(a = 2\), \(b = 2\), \(f(n) = n\)$  
  $\(n^{\log_b a} = n^{\log_2 2} = n^1 = n\)$  
- **Case 2:** $\(f(n) = \Theta(n \log^0 n)\)$  
- **Solution:**  
  $\[
  T(n) = \Theta(n \log n)
  \]$

### **Example 2: Binary Search**
- **Recurrence:** $\(T(n) = T(n/2) + 1\)$  
- **Parameters:**  
  $\(a = 1\), \(b = 2\), \(f(n) = 1\)$  
  $\(n^{\log_b a} = n^{\log_2 1} = n^0 = 1\)$  
- **Case 2:** $\(f(n) = \Theta(1 \log^0 n)\)$  
- **Solution:**  
  $\[
  T(n) = \Theta(\log n)
  \]$

### **Example 3: Strassen’s Algorithm**
- **Recurrence:** $\(T(n) = 7T(n/2) + n^2\)$  
- **Parameters:**  
  $\(a = 7\), \(b = 2\), \(f(n) = n^2\)$  
  $\(n^{\log_b a} = n^{\log_2 7} \approx n^{2.807}\)$  
- **Case 1:** $\(f(n) = O(n^{2.807 - \epsilon})\) (since \(2 < 2.807\))$  
- **Solution:**  
  $\[
  T(n) = \Theta(n^{\log_2 7})
  \]$

---

## **4. Implementation in C (Merge Sort Example)**
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
        mergeSort(arr, l, m);      // Recur on left half (Case 2)
        mergeSort(arr, m + 1, r);  // Recur on right half (Case 2)
        merge(arr, l, m, r);       // Combine (f(n) = O(n))
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

## **5. When Master Method Fails**
The Master Method **does not apply** if:
1. **Non-polynomial difference**:  
   - E.g., $\(T(n) = 2T(n/2) + n/\log n\)$.  
2. **Uneven splits**:  
   - E.g., $\(T(n) = T(n/3) + T(2n/3) + n\)$.  
3. **Wrong form**:  
   - E.g., $\(T(n) = T(n-1) + 1\)$ (not divide-and-conquer).  

**Alternatives:**  
- Use **recursion-tree** or **substitution method**.

---

## **6. Key Takeaways**
1. **Master Theorem** solves recurrences of the form $\(T(n) = aT(n/b) + f(n)\)$.  
2. **Three cases** based on $\(f(n)\) vs. \(n^{\log_b a}\)$.  
3. **Merge Sort** = $\(\Theta(n \log n)\)$, **Binary Search** = $\(\Theta(\log n)\)$.  

---

## **7. Practice Problem**
**Q:** Solve $\(T(n) = 3T(n/4) + n \log n\)$ using the Master Method.  
**Solution:**  
- $\(a = 3\), \(b = 4\), \(f(n) = n \log n\)$  
- $\(n^{\log_b a} = n^{\log_4 3} \approx n^{0.793}\)$  
- Compare $\(f(n)\) with \(n^{0.793}\)$:  
  $\(n \log n = \Omega(n^{0.793 + \epsilon})\)$ (Case 3)  
- Check regularity condition:  
  $\(3(n/4) \log(n/4) \leq cn \log n\) (holds for \(c \geq 3/4\))$  
- **Solution:** $\(T(n) = \Theta(n \log n)\)$  

---

