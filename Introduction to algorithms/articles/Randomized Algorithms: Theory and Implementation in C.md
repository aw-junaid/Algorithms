# **Randomized Algorithms: Theory and Implementation in C**

## **1. Introduction to Randomized Algorithms**
Randomized algorithms use **randomness** as part of their logic to achieve good performance **in expectation** or with **high probability**. They are particularly useful when:
- Deterministic algorithms are too complex
- Average-case performance matters more than worst-case
- We need to break symmetry in distributed systems

### **Key Advantages**
1. **Simpler** than deterministic counterparts
2. **Faster** in practice for many problems
3. **Elegant solutions** to complex problems

## **2. Classification of Randomized Algorithms**

### **(1) Las Vegas Algorithms**
- **Always correct**
- Running time is random
- Example: Randomized Quicksort

### **(2) Monte Carlo Algorithms**
- **May produce incorrect results** (with bounded probability)
- Running time is deterministic
- Example: Randomized primality testing

## **3. Fundamental Techniques**

### **(1) Random Sampling**
```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

void random_sample(int arr[], int n, int k) {
    srand(time(NULL));
    for (int i = 0; i < k; i++) {
        int j = i + rand() % (n - i);
        // Swap arr[i] and arr[j]
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
        printf("%d ", arr[i]);
    }
}

int main() {
    int arr[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    random_sample(arr, 10, 3);
    return 0;
}
```

### **(2) Random Permutation (Fisher-Yates Shuffle)**
```c
void fisher_yates_shuffle(int arr[], int n) {
    srand(time(NULL));
    for (int i = n-1; i > 0; i--) {
        int j = rand() % (i+1);
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

### **(3) Randomized Selection (Quickselect)**
```c
int partition(int arr[], int low, int high) {
    int pivot = arr[high];
    int i = low - 1;
    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }
    int temp = arr[i+1];
    arr[i+1] = arr[high];
    arr[high] = temp;
    return i+1;
}

int randomized_select(int arr[], int low, int high, int k) {
    if (low == high) return arr[low];
    
    int pivot_index = low + rand() % (high - low + 1);
    // Swap pivot to end
    int temp = arr[pivot_index];
    arr[pivot_index] = arr[high];
    arr[high] = temp;
    
    pivot_index = partition(arr, low, high);
    
    if (k == pivot_index) return arr[k];
    else if (k < pivot_index)
        return randomized_select(arr, low, pivot_index-1, k);
    else
        return randomized_select(arr, pivot_index+1, high, k);
}
```

## **4. Analysis Techniques**

### **(1) Expected Running Time**
For Randomized Quicksort:
$\[
E[T(n)] = O(n \log n)
\]$

### **(2) Probability of Error**
For Monte Carlo algorithms:
$\[
P(\text{Error}) \leq \frac{1}{n^c} \text{ for some constant } c > 0
\]$

## **5. Advanced Applications**

### **(1) Randomized Primality Test (Miller-Rabin)**
```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <math.h>

long long power(long long a, long long d, long long n) {
    long long result = 1;
    a = a % n;
    while (d > 0) {
        if (d % 2 == 1)
            result = (result * a) % n;
        d = d >> 1;
        a = (a * a) % n;
    }
    return result;
}

bool miller_test(long long d, long long n) {
    long long a = 2 + rand() % (n - 4);
    long long x = power(a, d, n);
    
    if (x == 1 || x == n-1)
        return true;
    
    while (d != n-1) {
        x = (x * x) % n;
        d *= 2;
        
        if (x == 1) return false;
        if (x == n-1) return true;
    }
    return false;
}

bool is_prime(long long n, int k) {
    if (n <= 1 || n == 4) return false;
    if (n <= 3) return true;
    
    long long d = n - 1;
    while (d % 2 == 0)
        d /= 2;
    
    for (int i = 0; i < k; i++)
        if (!miller_test(d, n))
            return false;
    
    return true;
}
```

### **(2) Skip Lists**
```c
typedef struct Node {
    int key;
    struct Node **forward;
} Node;

Node* create_node(int level, int key) {
    Node *n = malloc(sizeof(Node));
    n->forward = malloc(sizeof(Node*) * (level+1));
    n->key = key;
    return n;
}

int random_level(int max_level) {
    int level = 0;
    while (rand() < RAND_MAX/2 && level < max_level)
        level++;
    return level;
}
```

## **6. Practical Considerations**

### **(1) Random Number Generation**
- Use `srand(time(NULL))` to seed
- Better alternatives: `random()` or cryptographic RNGs for security

### **(2) Debugging**
- Set fixed seed for reproducibility:
  ```c
  srand(42);  // Fixed seed for debugging
  ```

## **7. Comparison with Deterministic Algorithms**

| Aspect              | Randomized Algorithms | Deterministic Algorithms |
|---------------------|-----------------------|--------------------------|
| **Worst-case**      | May be bad            | Guaranteed bounds        |
| **Average-case**    | Typically excellent   | Varies                   |
| **Implementation**  | Often simpler         | May be complex           |
| **Analysis**        | Probabilistic         | Exact                    |

## **8. When to Use Randomized Algorithms**

1. **Input distribution is unknown**
2. **Need simplicity and good average performance**
3. **Adversarial inputs are possible**
4. **Parallel/distributed environments**

## **Complete Example: Randomized Quicksort**
```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

void swap(int* a, int* b) {
    int t = *a;
    *a = *b;
    *b = t;
}

int partition(int arr[], int low, int high) {
    int pivot_index = low + rand() % (high - low + 1);
    int pivot = arr[pivot_index];
    swap(&arr[pivot_index], &arr[high]);
    
    int i = low;
    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            swap(&arr[i], &arr[j]);
            i++;
        }
    }
    swap(&arr[i], &arr[high]);
    return i;
}

void quick_sort(int arr[], int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quick_sort(arr, low, pi - 1);
        quick_sort(arr, pi + 1, high);
    }
}

int main() {
    srand(time(NULL));
    int arr[] = {10, 7, 8, 9, 1, 5};
    int n = sizeof(arr)/sizeof(arr[0]);
    
    quick_sort(arr, 0, n-1);
    
    printf("Sorted array: ");
    for (int i=0; i<n; i++)
        printf("%d ", arr[i]);
    
    return 0;
}
```


Randomized algorithms provide powerful tools for modern computing, especially in machine learning, cryptography, and large-scale data processing. Their probabilistic nature often leads to simpler, more efficient solutions than deterministic approaches.
