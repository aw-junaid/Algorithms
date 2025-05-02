# Sorting in Linear Time: Lower Bounds for Sorting

## Introduction to Sorting Lower Bounds

The study of sorting lower bounds tells us what is fundamentally possible and impossible in sorting algorithms. A key result in computer science is that comparison-based sorting algorithms cannot do better than Ω(n log n) time in the worst case.

## Comparison-Based Sorting

In comparison-based sorting:
- Algorithms only use comparisons between elements to gain information about their order
- No other information about the elements is used (like their numeric values or bit representations)
- Examples: Merge Sort, Quick Sort, Heap Sort, Bubble Sort, Insertion Sort

## The Ω(n log n) Lower Bound

**Theorem**: Any comparison-based sorting algorithm requires Ω(n log n) comparisons in the worst case to sort n elements.

### Proof Using Decision Trees

1. **Decision Tree Model**: 
   - Every comparison-based sort can be represented as a decision tree
   - Each internal node represents a comparison (a ≤ b?)
   - Each leaf represents a complete ordering of the elements

2. **Number of Leaves**:
   - For n distinct elements, there are n! possible permutations
   - The decision tree must have at least n! leaves to account for all possible outcomes

3. **Tree Height**:
   - A binary tree with L leaves has height at least log₂L
   - Therefore, the height is at least log₂(n!)
   - Using Stirling's approximation: n! ≈ (n/e)ⁿ√(2πn)
   - So log₂(n!) ≈ n log₂n - n log₂e + O(log n) = Ω(n log n)

4. **Conclusion**:
   - The longest path from root to leaf (worst case) must be at least Ω(n log n)
   - Therefore, any comparison-based sort requires Ω(n log n) comparisons in worst case

## Breaking the Ω(n log n) Barrier

We can sort in O(n) time (linear time) if we:
1. Don't use comparisons as the primary operation, or
2. Have additional information about the elements

### Non-Comparison-Based Sorts

These algorithms use information about the elements beyond just comparisons:

1. **Counting Sort**:
   - Assumes elements are integers in a specific range
   - Works by counting occurrences of each element

2. **Radix Sort**:
   - Sorts numbers digit by digit (or by groups of bits)
   - Needs a stable subroutine sort for each digit

3. **Bucket Sort**:
   - Assumes input is uniformly distributed over a range
   - Divides range into buckets and sorts each bucket

## Counting Sort Implementation in C

Here's a C implementation of Counting Sort, which runs in O(n + k) time where k is the range of input values:

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void countingSort(int array[], int size) {
    // Find the maximum element to determine the range
    int max = array[0];
    for (int i = 1; i < size; i++) {
        if (array[i] > max) {
            max = array[i];
        }
    }

    // Create a count array initialized to zeros
    int *count = (int *)calloc(max + 1, sizeof(int));
    
    // Store the count of each element
    for (int i = 0; i < size; i++) {
        count[array[i]]++;
    }

    // Modify the count array to store cumulative counts
    for (int i = 1; i <= max; i++) {
        count[i] += count[i - 1];
    }

    // Create the output array
    int *output = (int *)malloc(size * sizeof(int));
    
    // Place the elements in sorted order
    for (int i = size - 1; i >= 0; i--) {
        output[count[array[i]] - 1] = array[i];
        count[array[i]]--;
    }

    // Copy the sorted elements back to original array
    for (int i = 0; i < size; i++) {
        array[i] = output[i];
    }

    // Free allocated memory
    free(count);
    free(output);
}

// Function to print an array
void printArray(int array[], int size) {
    for (int i = 0; i < size; i++) {
        printf("%d ", array[i]);
    }
    printf("\n");
}

// Driver code
int main() {
    int array[] = {4, 2, 2, 8, 3, 3, 1};
    int size = sizeof(array) / sizeof(array[0]);
    
    printf("Original array: ");
    printArray(array, size);
    
    countingSort(array, size);
    
    printf("Sorted array: ");
    printArray(array, size);
    
    return 0;
}
```

### Explanation of Counting Sort

1. **Find the maximum value**: Determines the range needed for our count array.
2. **Initialize count array**: Creates an array to store counts of each value.
3. **Count occurrences**: Populates the count array with frequency of each element.
4. **Cumulative counts**: Modifies the count array to contain positions of elements.
5. **Build output array**: Places elements in their correct positions in a new array.
6. **Copy back**: Transfers the sorted elements back to the original array.

## Radix Sort Implementation in C

Here's a C implementation of Radix Sort:

```c
#include <stdio.h>
#include <stdlib.h>

// A utility function to get the maximum value in array
int getMax(int array[], int n) {
    int max = array[0];
    for (int i = 1; i < n; i++)
        if (array[i] > max)
            max = array[i];
    return max;
}

// A function to do counting sort of array[] according to
// the digit represented by exp (10^i where i is current digit number)
void countSort(int array[], int n, int exp) {
    int output[n]; // output array
    int count[10] = {0};

    // Store count of occurrences in count[]
    for (int i = 0; i < n; i++)
        count[(array[i] / exp) % 10]++;

    // Change count[i] so that count[i] now contains actual
    // position of this digit in output[]
    for (int i = 1; i < 10; i++)
        count[i] += count[i - 1];

    // Build the output array
    for (int i = n - 1; i >= 0; i--) {
        output[count[(array[i] / exp) % 10] - 1] = array[i];
        count[(array[i] / exp) % 10]--;
    }

    // Copy the output array to array[], so that array[] now
    // contains sorted numbers according to current digit
    for (int i = 0; i < n; i++)
        array[i] = output[i];
}

// The main function to implement Radix Sort
void radixSort(int array[], int n) {
    // Find the maximum number to know number of digits
    int max = getMax(array, n);

    // Do counting sort for every digit
    for (int exp = 1; max / exp > 0; exp *= 10)
        countSort(array, n, exp);
}

// A utility function to print an array
void printArray(int array[], int n) {
    for (int i = 0; i < n; i++)
        printf("%d ", array[i]);
    printf("\n");
}

// Driver code
int main() {
    int array[] = {170, 45, 75, 90, 802, 24, 2, 66};
    int n = sizeof(array) / sizeof(array[0]);

    printf("Original array: ");
    printArray(array, n);

    radixSort(array, n);

    printf("Sorted array: ");
    printArray(array, n);
    return 0;
}
```

### Explanation of Radix Sort

1. **Find maximum number**: Determines the number of digits needed.
2. **Digit-by-digit sort**: Sorts numbers from least significant digit to most.
3. **Stable sorting**: Uses counting sort as a stable subroutine for each digit.
4. **Complexity**: O(d*(n + b)) where d is number of digits, b is base (usually 10).

## Key Takeaways

1. **Comparison-based sorting** has a fundamental lower bound of Ω(n log n).
2. **Non-comparison sorts** can achieve O(n) time by exploiting specific properties of the data:
   - Counting Sort: When range of elements (k) is O(n)
   - Radix Sort: When numbers have a fixed number of digits
   - Bucket Sort: When data is uniformly distributed
3. **Trade-offs**: Linear-time sorts often require additional memory and have constraints on input data.

These algorithms demonstrate how understanding problem constraints can lead to more efficient solutions than what's possible in the general case.
