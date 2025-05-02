# Radix Sort: A Linear-Time Integer Sorting Algorithm

Radix sort is a non-comparative sorting algorithm that processes integer digits or other digit-based keys in multiple passes. It achieves O(n) time complexity when the digit size (or key size) is constant, making it exceptionally efficient for sorting integers or fixed-length strings.

## How Radix Sort Works

Radix sort works by processing digits from the least significant digit (LSD) to the most significant digit (MSD) or vice versa, using a stable subroutine sort (typically counting sort) at each digit position.

### Key Characteristics:
- **Time Complexity**: O(d*(n + b)) where d is digit count, b is base (usually 10)
- **Space Complexity**: O(n + b)
- **Stable Sort**: When using stable subroutines
- **Non-Comparison Based**: Sorts by digit positions
- **Integer/String Sorting**: Works best with fixed-length data

## Step-by-Step Algorithm

1. **Find Maximum Number**: Determine the number of digits needed
2. **Digit-by-Digit Sort**: From LSD to MSD
3. **Counting Sort Subroutine**: Stable sort for each digit position
4. **Repeat**: Until all digits are processed

## Detailed Implementation in C

```c
#include <stdio.h>
#include <stdlib.h>

// Utility function to get maximum value in array
int getMax(int arr[], int n) {
    int max = arr[0];
    for (int i = 1; i < n; i++)
        if (arr[i] > max)
            max = arr[i];
    return max;
}

// Counting sort subroutine for radix sort
void countingSort(int arr[], int n, int exp) {
    int output[n]; // Output array
    int count[10] = {0}; // Initialize count array (base 10)

    // Store count of occurrences
    for (int i = 0; i < n; i++)
        count[(arr[i] / exp) % 10]++;

    // Change count[i] to store cumulative count
    for (int i = 1; i < 10; i++)
        count[i] += count[i - 1];

    // Build the output array (processing right to left for stability)
    for (int i = n - 1; i >= 0; i--) {
        output[count[(arr[i] / exp) % 10] - 1] = arr[i];
        count[(arr[i] / exp) % 10]--;
    }

    // Copy output array to original array
    for (int i = 0; i < n; i++)
        arr[i] = output[i];
}

// Main radix sort function
void radixSort(int arr[], int n) {
    // Find maximum number to know number of digits
    int max = getMax(arr, n);

    // Do counting sort for every digit (exp is 10^i)
    for (int exp = 1; max / exp > 0; exp *= 10)
        countingSort(arr, n, exp);
}

// Utility function to print array
void printArray(int arr[], int n) {
    for (int i = 0; i < n; i++)
        printf("%d ", arr[i]);
    printf("\n");
}

// Driver program
int main() {
    int arr[] = {170, 45, 75, 90, 802, 24, 2, 66};
    int n = sizeof(arr) / sizeof(arr[0]);

    printf("Original array: ");
    printArray(arr, n);

    radixSort(arr, n);

    printf("Sorted array: ");
    printArray(arr, n);

    return 0;
}
```

## Detailed Explanation of Each Component

### 1. Finding the Maximum Value
```c
int getMax(int arr[], int n) {
    int max = arr[0];
    for (int i = 1; i < n; i++)
        if (arr[i] > max)
            max = arr[i];
    return max;
}
```
- Scans the entire array to find the maximum value
- Determines how many digit positions need processing

### 2. Counting Sort Subroutine
```c
void countingSort(int arr[], int n, int exp) {
    // ... (implementation as shown above)
}
```
- Processes a specific digit position (units, tens, hundreds, etc.)
- Uses counting sort to sort based on the current digit
- Maintains stability by processing right to left

### 3. Main Radix Sort Function
```c
void radixSort(int arr[], int n) {
    int max = getMax(arr, n);
    for (int exp = 1; max / exp > 0; exp *= 10)
        countingSort(arr, n, exp);
}
```
- Finds maximum number to determine digit count
- Processes each digit position from LSD to MSD
- Uses powers of 10 (1, 10, 100, ...) to isolate each digit

### 4. Digit Extraction
```c
(arr[i] / exp) % 10
```
- Mathematical operation to extract the current digit
- `exp` is the current digit position (1, 10, 100, etc.)
- `% 10` gives the digit at that position

## Advantages of Radix Sort

1. **Linear Time Complexity**: O(dn) where d is constant for fixed-width data
2. **Stable Sorting**: Maintains relative order of equal keys
3. **Efficient for Large Data**: When d is small compared to n
4. **Integer/String Sorting**: Works well for fixed-length data types

## Limitations of Radix Sort

1. **Fixed Data Width Requirement**: Needs known maximum number of digits
2. **Memory Usage**: Requires additional space for counting sort
3. **Integer Restriction**: Primarily for integers or fixed-length strings
4. **Base Dependency**: Performance depends on choice of base/radix

## When to Use Radix Sort

Radix sort is ideal when:
- Sorting integers with a limited number of digits
- Sorting fixed-length strings
- Stability is required
- The range of values is known in advance

## Example Walkthrough

Sorting: [170, 45, 75, 90, 802, 24, 2, 66]

### Pass 1 (Units place):
Original: 170, 045, 075, 090, 802, 024, 002, 066
Sorted: 170, 090, 802, 002, 024, 045, 075, 066

### Pass 2 (Tens place):
Original: 170, 090, 802, 002, 024, 045, 075, 066
Sorted: 802, 002, 024, 045, 066, 170, 075, 090

### Pass 3 (Hundreds place):
Original: 802, 002, 024, 045, 066, 170, 075, 090
Sorted: 002, 024, 045, 066, 075, 090, 170, 802

Final sorted array: [2, 24, 45, 66, 75, 90, 170, 802]

## Optimizations and Variations

1. **MSD Radix Sort**: Processes digits from most significant to least
2. **Hybrid Approaches**: Combine with other sorts for better performance
3. **Different Bases**: Use base 256 for byte-level processing
4. **In-Place Variants**: Reduce memory usage at cost of stability

This implementation provides a complete, efficient radix sort with clear digit-by-digit processing and proper use of counting sort as a stable subroutine. The code includes all necessary components with detailed comments explaining each step.
