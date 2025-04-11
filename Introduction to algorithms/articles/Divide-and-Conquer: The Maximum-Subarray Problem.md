# **Divide-and-Conquer: The Maximum-Subarray Problem**

The **Maximum-Subarray Problem** is a classic algorithmic challenge where we find the contiguous subarray within a one-dimensional array of numbers that has the largest sum. This problem has applications in finance (e.g., stock price analysis), computer vision, and data mining.

## **1. Problem Definition**
Given an array of integers (which can include negative numbers), find the contiguous subarray with the **maximum sum**.

**Example:**  
Input: `[-2, 1, -3, 4, -1, 2, 1, -5, 4]`  
Output: `[4, -1, 2, 1]` (Sum = **6**)  

---

## **2. Brute-Force Approach (Naive Solution)**
A brute-force solution checks every possible subarray and computes its sum.  
- **Time Complexity:** $\(O(n^2)\)$ (Inefficient for large arrays).  

### **Pseudocode (Brute-Force)**
```plaintext
max_sum = -∞
for i from 0 to n-1:
    current_sum = 0
    for j from i to n-1:
        current_sum += A[j]
        if current_sum > max_sum:
            max_sum = current_sum
            start = i
            end = j
return (max_sum, start, end)
```

---

## **3. Divide-and-Conquer Approach (Efficient Solution)**
The **Divide-and-Conquer** strategy breaks the problem into smaller subproblems, solves them recursively, and combines their solutions.

### **Key Steps**
1. **Divide:** Split the array into two halves.
2. **Conquer:** Recursively find the maximum subarrays in the left and right halves.
3. **Combine:** Find the maximum subarray crossing the midpoint and compare it with the left and right solutions.

### **Time Complexity:** $\(O(n \log n)\)$ (Faster than brute-force).

---

## **4. Detailed Explanation of Divide-and-Conquer**
### **(1) Base Case**
If the array has only one element, that element is the maximum subarray.

### **(2) Recursive Case**
1. **Divide:** Split the array into `left` and `right` halves.
2. **Conquer:**  
   - Find the maximum subarray in the `left` half.  
   - Find the maximum subarray in the `right` half.  
3. **Combine:**  
   - Find the maximum subarray **crossing the midpoint**.  
   - Compare the three results (left, right, crossing) and return the best one.

### **(3) Finding the Maximum Crossing Subarray**
- Start from the midpoint and expand left and right to find the best possible subarray.
- **Time Complexity for Crossing Subarray:** $\(O(n)\)$.

---

## **5. Algorithm Implementation in C**
```c
#include <stdio.h>
#include <limits.h>

// Structure to store subarray details
typedef struct {
    int low;
    int high;
    int sum;
} Subarray;

// Function to find maximum crossing subarray
Subarray maxCrossingSubarray(int A[], int low, int mid, int high) {
    int left_sum = INT_MIN;
    int sum = 0;
    int max_left = mid;

    for (int i = mid; i >= low; i--) {
        sum += A[i];
        if (sum > left_sum) {
            left_sum = sum;
            max_left = i;
        }
    }

    int right_sum = INT_MIN;
    sum = 0;
    int max_right = mid + 1;

    for (int j = mid + 1; j <= high; j++) {
        sum += A[j];
        if (sum > right_sum) {
            right_sum = sum;
            max_right = j;
        }
    }

    Subarray cross = {max_left, max_right, left_sum + right_sum};
    return cross;
}

// Function to find maximum subarray (Divide-and-Conquer)
Subarray maxSubarray(int A[], int low, int high) {
    if (low == high) {
        Subarray single = {low, high, A[low]};
        return single;
    }

    int mid = (low + high) / 2;

    // Find max subarrays in left, right, and crossing
    Subarray left = maxSubarray(A, low, mid);
    Subarray right = maxSubarray(A, mid + 1, high);
    Subarray cross = maxCrossingSubarray(A, low, mid, high);

    // Return the subarray with the largest sum
    if (left.sum >= right.sum && left.sum >= cross.sum) {
        return left;
    } else if (right.sum >= left.sum && right.sum >= cross.sum) {
        return right;
    } else {
        return cross;
    }
}

int main() {
    int A[] = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
    int n = sizeof(A) / sizeof(A[0]);

    Subarray result = maxSubarray(A, 0, n - 1);

    printf("Maximum Subarray Sum: %d\n", result.sum);
    printf("Subarray: [");
    for (int i = result.low; i <= result.high; i++) {
        printf("%d", A[i]);
        if (i < result.high) printf(", ");
    }
    printf("]\n");

    return 0;
}
```

---

## **6. Output Explanation**
**Input:** `[-2, 1, -3, 4, -1, 2, 1, -5, 4]`  
**Output:**  
```
Maximum Subarray Sum: 6  
Subarray: [4, -1, 2, 1]  
```

### **How It Works**
1. **Divide:** Splits the array into smaller subarrays until single elements remain.  
2. **Conquer:** Recursively finds the best subarrays in left and right halves.  
3. **Combine:** Checks the crossing subarray and picks the best of the three.  

---

## **7. Time Complexity Analysis**
| Step               | Complexity  |
|--------------------|------------|
| Divide             | $\(O(1)\)$   |
| Conquer (Recursion)| $\(2T(n/2)\)$|
| Combine (Crossing) | $\(O(n)\)$   |
| **Total**          | $\(O(n \log n)\)$ |

---

## **8. Comparison with Other Approaches**
| Method            | Time Complexity | Space Complexity | Notes                     |
|-------------------|----------------|------------------|--------------------------|
| Brute Force       | $\(O(n^2)\)$     | $\(O(1)\)$         | Simple but slow          |
| Divide-and-Conquer| $\(O(n \log n)\)$| $\(O(\log n)\)$ (stack) | Efficient for large arrays |
| Kadane’s Algorithm| $\(O(n)\)$       | $\(O(1)\)$        | **Optimal** (Dynamic Programming) |

---

## **9. Kadane’s Algorithm (Optimal Solution)**
For completeness, here’s the **Kadane’s Algorithm** (Dynamic Programming approach) which solves the problem in \(O(n)\) time:
```c
int maxSubarrayKadane(int A[], int n) {
    int max_current = A[0];
    int max_global = A[0];

    for (int i = 1; i < n; i++) {
        max_current = (A[i] > max_current + A[i]) ? A[i] : max_current + A[i];
        if (max_current > max_global) {
            max_global = max_current;
        }
    }
    return max_global;
}
```

---

## **10. Conclusion**
- **Divide-and-Conquer** provides an efficient $(\(O(n \log n)\))$ solution.
- **Kadane’s Algorithm** is even better $(\(O(n)\))$ for large datasets.
- The problem is fundamental in algorithm design and optimization.
