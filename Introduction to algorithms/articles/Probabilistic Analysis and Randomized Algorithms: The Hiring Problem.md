# **Probabilistic Analysis and Randomized Algorithms: The Hiring Problem**

## **1. Introduction to the Hiring Problem**
The **hiring problem** is a classic example in **probabilistic analysis** and **randomized algorithms**. It models the cost of hiring an employee in an interview process, where:
- **Interviewing** has a low cost.
- **Hiring** has a high cost (e.g., firing the previous candidate and hiring a new one).

### **Problem Statement**
- **Input:** A sequence of `n` candidates with distinct quality scores.
- **Goal:** Hire the **best candidate** with **minimal hiring cost**.

---

## **2. Key Concepts**
### **(1) Deterministic Hiring Algorithm**
- Interview candidates one by one.
- **Hire if the current candidate is better than all previous ones.**
- **Worst-case cost:** $\(O(n \cdot c_h)\)$ (if candidates arrive in increasing order, hire every time).

### **(2) Probabilistic Analysis**
- **Assume candidates arrive in random order.**
- **Expected hiring cost** = $\(O(c_h \log n + c_i n)\)$, where:
  - $\(c_h\)$ = cost of hiring,
  - $\(c_i\)$ = cost of interviewing.

### **(3) Randomized Algorithm**
- **Randomly permute the candidate order** to ensure uniform randomness.
- **Expected hiring cost** improves to $\(O(\log n)\)$ hires.

---

## **3. Mathematical Analysis**
### **(1) Expected Number of Hires**
- Let \(X\) = number of hires.
- $\(X = X_1 + X_2 + \dots + X_n\)$, where:
```math
  X_i = 
  \begin{cases} 
  1 & \text{if the } i\text{-th candidate is hired}, \\
  0 & \text{otherwise.}
  \end{cases}
```
- **Probability that the \(i\)-th candidate is the best so far:** $\(\frac{1}{i}\)$ (since the first \(i\) candidates are randomly ordered).
- **Expected hires:**
  $\[
  E[X] = \sum_{i=1}^n \frac{1}{i} = H_n \approx \ln n + O(1)
  \]$
  (where $\(H_n\)$ is the $\(n\)$-th **harmonic number**).

### **(2) Total Expected Cost**
- **Interview cost:** $\(n \cdot c_i\)$ (always interview all candidates).
- **Hiring cost:** $\(E[X] \cdot c_h \approx c_h \ln n\)$.
- **Total cost:** $\(O(c_i n + c_h \log n)\)$.

---

## **4. Implementation in C**
### **(1) Deterministic Hiring Algorithm**
```c
#include <stdio.h>
#include <limits.h>

void deterministicHiring(int candidates[], int n, int ch, int ci) {
    int best = INT_MIN;
    int hires = 0;
    int total_cost = 0;

    for (int i = 0; i < n; i++) {
        total_cost += ci; // Interview cost
        if (candidates[i] > best) {
            best = candidates[i];
            total_cost += ch; // Hiring cost
            hires++;
            printf("Hired candidate %d (Quality: %d)\n", i+1, candidates[i]);
        }
    }

    printf("Total hires: %d\n", hires);
    printf("Total cost: %d\n", total_cost);
}

int main() {
    int candidates[] = {3, 2, 5, 1, 4, 7, 6};
    int n = sizeof(candidates) / sizeof(candidates[0]);
    int ch = 10; // Hiring cost
    int ci = 1;  // Interview cost

    deterministicHiring(candidates, n, ch, ci);
    return 0;
}
```

### **(2) Randomized Hiring Algorithm**
```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <limits.h>

void shuffle(int arr[], int n) {
    srand(time(NULL));
    for (int i = n-1; i > 0; i--) {
        int j = rand() % (i+1);
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}

void randomizedHiring(int candidates[], int n, int ch, int ci) {
    shuffle(candidates, n); // Randomize the order
    deterministicHiring(candidates, n, ch, ci); // Same hiring logic
}

int main() {
    int candidates[] = {3, 2, 5, 1, 4, 7, 6};
    int n = sizeof(candidates) / sizeof(candidates[0]);
    int ch = 10; // Hiring cost
    int ci = 1;  // Interview cost

    randomizedHiring(candidates, n, ch, ci);
    return 0;
}
```

---

## **5. Key Takeaways**
1. **Deterministic approach** can have high worst-case cost $(\(O(n)\)$ hires).
2. **Randomized algorithm** reduces expected hires to $\(O(\log n)\)$.
3. **Probabilistic analysis** helps estimate average-case behavior.

---

