# Medians and Order Statistics: Minimum and Maximum

## Introduction to Order Statistics

Order statistics are the elements of a set when they are sorted in increasing order. The ith order statistic of a set is the ith smallest element. Two fundamental order statistics are:

1. **Minimum**: The 1st order statistic (smallest element)
2. **Maximum**: The nth order statistic (largest element) in a set of n elements

## Minimum and Maximum

### Finding the Minimum

The minimum of a set is the element with the smallest value. To find it, we need to examine each element and keep track of the smallest one found so far.

### Finding the Maximum

Similarly, the maximum is the element with the largest value, found by examining each element and keeping track of the largest one found so far.

### Time Complexity

Both minimum and maximum can be found in:
- **Θ(n) time** for a set of n elements
- This is optimal since each element must be examined at least once

## Simultaneous Minimum and Maximum

It's possible to find both min and max in:
- **3⌊n/2⌋ comparisons** (better than the obvious 2n-2 comparisons)
- The approach compares elements in pairs

## Implementation in C

Here's a complete implementation in C that finds both minimum and maximum:

```c
#include <stdio.h>
#include <limits.h>

// Structure to hold both min and max
struct MinMax {
    int min;
    int max;
};

// Function to find minimum and maximum in an array
struct MinMax findMinMax(int arr[], int n) {
    struct MinMax result;
    int i;
    
    // Initialize min and max
    if (n % 2 == 0) {
        // Even number of elements - compare first two
        if (arr[0] > arr[1]) {
            result.max = arr[0];
            result.min = arr[1];
        } else {
            result.min = arr[0];
            result.max = arr[1];
        }
        i = 2; // Start from third element
    } else {
        // Odd number of elements - initialize both with first element
        result.min = result.max = arr[0];
        i = 1; // Start from second element
    }
    
    // Compare remaining elements in pairs
    while (i < n - 1) {
        if (arr[i] > arr[i+1]) {
            if (arr[i] > result.max) {
                result.max = arr[i];
            }
            if (arr[i+1] < result.min) {
                result.min = arr[i+1];
            }
        } else {
            if (arr[i+1] > result.max) {
                result.max = arr[i+1];
            }
            if (arr[i] < result.min) {
                result.min = arr[i];
            }
        }
        i += 2; // Move to next pair
    }
    
    return result;
}

int main() {
    int arr[] = {1000, 11, 445, 1, 330, 3000};
    int n = sizeof(arr) / sizeof(arr[0]);
    
    struct MinMax result = findMinMax(arr, n);
    
    printf("Minimum element is %d\n", result.min);
    printf("Maximum element is %d\n", result.max);
    
    return 0;
}
```

## Explanation of the Algorithm

1. **Initialization**:
   - For even n: Compare first two elements to initialize min and max
   - For odd n: Initialize both min and max with the first element

2. **Processing Elements in Pairs**:
   - Compare elements two at a time
   - First compare them to each other
   - Then compare the larger with current max and the smaller with current min
   - This reduces the number of comparisons to 3 per 2 elements (instead of 4)

3. **Advantages**:
   - Total comparisons: 3n/2 - 2 (better than the naive 2n - 2 approach)
   - Still O(n) time complexity but with a better constant factor

## Alternative Simple Implementation

Here's a simpler version that finds min and max separately (less efficient but easier to understand):

```c
#include <stdio.h>
#include <limits.h>

int findMin(int arr[], int n) {
    int min = INT_MAX;
    for (int i = 0; i < n; i++) {
        if (arr[i] < min) {
            min = arr[i];
        }
    }
    return min;
}

int findMax(int arr[], int n) {
    int max = INT_MIN;
    for (int i = 0; i < n; i++) {
        if (arr[i] > max) {
            max = arr[i];
        }
    }
    return max;
}

int main() {
    int arr[] = {1000, 11, 445, 1, 330, 3000};
    int n = sizeof(arr) / sizeof(arr[0]);
    
    printf("Minimum element is %d\n", findMin(arr, n));
    printf("Maximum element is %d\n", findMax(arr, n));
    
    return 0;
}
```

## Applications of Min/Max Finding

1. Database systems (query optimization)
2. Statistics and data analysis
3. Computer graphics (bounding boxes)
4. Machine learning (normalization)
5. Scheduling algorithms

The more efficient pairwise comparison method is particularly useful when dealing with large datasets where performance is critical.
