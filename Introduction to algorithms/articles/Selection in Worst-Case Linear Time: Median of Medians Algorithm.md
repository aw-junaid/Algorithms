# Selection in Worst-Case Linear Time: Median of Medians Algorithm

## Introduction to Worst-Case Linear Time Selection

While randomized selection provides expected O(n) time, some applications require guaranteed O(n) performance. The deterministic "Median of Medians" algorithm achieves this by carefully selecting pivots that ensure good partitions.

## Key Concepts

1. **Median of Medians**: A pivot selection strategy that guarantees balanced partitions
2. **Deterministic**: No randomization - always O(n) time
3. **Complexity**: O(n) worst-case time but with larger constant factors than randomized version

## Median of Medians Algorithm

### Algorithm Steps:

1. **Divide** the array into groups of 5 elements
2. **Find median** of each group (using sorting)
3. **Recursively find median** of these medians
4. **Partition** using this median-of-medians as pivot
5. **Recurse** on the appropriate partition

### Why This Works:

- The median-of-medians ensures at least 30% of elements are on each side of the pivot
- This guarantees logarithmic recursion depth
- Total work forms a geometric series summing to O(n)

## Implementation in C

```c
#include <stdio.h>
#include <stdlib.h>

// Swap two elements
void swap(int* a, int* b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

// Insertion sort for small arrays
void insertionSort(int arr[], int left, int right) {
    for (int i = left + 1; i <= right; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= left && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}

// Partition function similar to quicksort
int partition(int arr[], int low, int high, int pivotValue) {
    // Find pivot index and swap with last element
    int pivotIndex;
    for (pivotIndex = low; pivotIndex < high; pivotIndex++) {
        if (arr[pivotIndex] == pivotValue) break;
    }
    swap(&arr[pivotIndex], &arr[high]);
    
    // Standard partition
    int i = low - 1;
    for (int j = low; j <= high - 1; j++) {
        if (arr[j] <= pivotValue) {
            i++;
            swap(&arr[i], &arr[j]);
        }
    }
    swap(&arr[i + 1], &arr[high]);
    return i + 1;
}

// Find median of medians
int findMedianOfMedians(int arr[], int left, int right) {
    if (right - left < 5) {
        insertionSort(arr, left, right);
        return arr[left + (right - left) / 2];
    }
    
    // Divide into groups of 5 and find their medians
    for (int i = left; i <= right; i += 5) {
        int subRight = i + 4;
        if (subRight > right) subRight = right;
        
        insertionSort(arr, i, subRight);
        
        // Move medians to front of array
        int medianPos = i + (subRight - i) / 2;
        swap(&arr[medianPos], &arr[left + (i - left)/5]);
    }
    
    // Recursively find median of medians
    int mid = left + (right - left) / 10;
    return deterministicSelect(arr, left, left + (right - left)/5, mid);
}

// Main deterministic select function
int deterministicSelect(int arr[], int left, int right, int k) {
    if (left == right) return arr[left];
    
    int pivotValue = findMedianOfMedians(arr, left, right);
    int pivotIndex = partition(arr, left, right, pivotValue);
    int pos = pivotIndex - left + 1;
    
    if (k == pos) {
        return arr[pivotIndex];
    } else if (k < pos) {
        return deterministicSelect(arr, left, pivotIndex - 1, k);
    } else {
        return deterministicSelect(arr, pivotIndex + 1, right, k - pos);
    }
}

int main() {
    int arr[] = {12, 3, 5, 7, 4, 19, 26, 31, 8, 14, 17, 21};
    int n = sizeof(arr) / sizeof(arr[0]);
    int k = 5; // Find the 5th smallest element
    
    printf("Array: ");
    for (int i = 0; i < n; i++) printf("%d ", arr[i]);
    printf("\n");
    
    int result = deterministicSelect(arr, 0, n - 1, k);
    printf("The %dth smallest element is %d\n", k, result);
    
    return 0;
}
```

## Detailed Explanation

### 1. Median of Medians Selection

1. Divide array into ⌈n/5⌉ groups of 5 elements
2. Sort each group (using insertion sort)
3. Extract medians from each group
4. Recursively find median of these medians

### 2. Partitioning

1. Use median-of-medians as pivot
2. Partition array around this pivot
3. Determine which partition contains the desired order statistic

### 3. Recursion

1. If pivot is the desired element, return it
2. Otherwise recurse on the appropriate partition
3. Adjust the order statistic index when recursing on right partition

## Complexity Analysis

- **Grouping**: O(n) time to create groups
- **Sorting groups**: O(n) since each group has fixed size (5)
- **Median of medians**: T(n/5)
- **Partitioning**: O(n)
- **Recursion**: T(7n/10) (since pivot guarantees at least 30% on each side)

Recurrence relation: T(n) ≤ T(n/5) + T(7n/10) + O(n)

This solves to O(n) by the Akra-Bazzi method or induction.

## Practical Considerations

1. **Constant Factors**: Larger than randomized version
2. **Group Size**: 5 is optimal - balances recursion costs
3. **Insertion Sort**: Efficient for small fixed-size groups
4. **Stability**: Not stable (like most selection algorithms)

## Comparison with Randomized Select

| Feature            | Randomized Select | Median of Medians |
|--------------------|-------------------|-------------------|
| Time Complexity    | Expected O(n)     | Worst-case O(n)   |
| Constant Factors   | Smaller           | Larger            |
| Randomness         | Requires PRNG     | Deterministic     |
| Implementation     | Simpler           | More complex      |

## Optimized Implementation with Edge Cases

```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

void swap(int* a, int* b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

void insertionSort(int arr[], int left, int right) {
    for (int i = left + 1; i <= right; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= left && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}

int medianOfFive(int a, int b, int c, int d, int e) {
    int arr[5] = {a, b, c, d, e};
    insertionSort(arr, 0, 4);
    return arr[2];
}

int findMedianOfMedians(int arr[], int left, int right) {
    if (right - left < 5) {
        insertionSort(arr, left, right);
        return arr[left + (right - left)/2];
    }

    // Move medians to front of array
    for (int i = left; i <= right; i += 5) {
        int subRight = i + 4;
        if (subRight > right) subRight = right;
        
        int median;
        if (subRight - i == 4) {
            median = medianOfFive(arr[i], arr[i+1], arr[i+2], arr[i+3], arr[i+4]);
        } else {
            insertionSort(arr, i, subRight);
            median = arr[i + (subRight - i)/2];
        }
        
        swap(&arr[median], &arr[left + (i - left)/5]);
    }
    
    // Compute the number of medians
    int numMedians = (right - left)/5 + 1;
    return deterministicSelect(arr, left, left + numMedians - 1, left + numMedians/2);
}

int partition(int arr[], int left, int right, int pivotValue) {
    // Find pivot index
    int pivotIndex;
    for (pivotIndex = left; pivotIndex <= right; pivotIndex++) {
        if (arr[pivotIndex] == pivotValue) break;
    }
    swap(&arr[pivotIndex], &arr[right]);
    
    // Standard partition
    int storeIndex = left;
    for (int i = left; i < right; i++) {
        if (arr[i] < pivotValue) {
            swap(&arr[storeIndex], &arr[i]);
            storeIndex++;
        }
    }
    swap(&arr[right], &arr[storeIndex]);
    return storeIndex;
}

int deterministicSelect(int arr[], int left, int right, int k) {
    while (left < right) {
        if (right - left < 5) {
            insertionSort(arr, left, right);
            return arr[left + k - 1];
        }
        
        int pivotValue = findMedianOfMedians(arr, left, right);
        int pivotIndex = partition(arr, left, right, pivotValue);
        int pos = pivotIndex - left + 1;
        
        if (k == pos) {
            return arr[pivotIndex];
        } else if (k < pos) {
            right = pivotIndex - 1;
        } else {
            left = pivotIndex + 1;
            k -= pos;
        }
    }
    return arr[left];
}

int main() {
    int testCases[][10] = {
        {12, 3, 5, 7, 4, 19, 26, 31, 8, 14},
        {1, 2, 3, 4, 5, 6, 7, 8, 9, 10},
        {10, 9, 8, 7, 6, 5, 4, 3, 2, 1},
        {42},
        {5, 5, 5, 5, 5, 5, 5, 5, 5, 5}
    };
    int sizes[] = {10, 10, 10, 1, 10};
    
    for (int t = 0; t < 5; t++) {
        printf("\nTest Case %d: ", t+1);
        for (int i = 0; i < sizes[t]; i++) printf("%d ", testCases[t][i]);
        printf("\n");
        
        for (int k = 1; k <= sizes[t]; k++) {
            // Create a copy since the algorithm modifies the array
            int arrCopy[10];
            for (int i = 0; i < sizes[t]; i++) arrCopy[i] = testCases[t][i];
            
            int result = deterministicSelect(arrCopy, 0, sizes[t]-1, k);
            printf(" %dth smallest: %d\n", k, result);
        }
    }
    
    return 0;
}
```

This enhanced implementation includes:
- Better median calculation for small groups
- Iterative implementation (reduces recursion depth)
- Comprehensive test cases
- Handles duplicates and edge cases
- Preserves original array by working on copies

The median-of-medians algorithm is essential when worst-case performance matters, such as in real-time systems or when adversarial inputs are possible. While more complex than randomized selection, its guaranteed O(n) performance makes it valuable for critical applications.
