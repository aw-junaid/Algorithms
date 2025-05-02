# Counting Sort: A Linear-Time Sorting Algorithm

Counting sort is a non-comparison based sorting algorithm that operates in O(n + k) time, where n is the number of elements and k is the range of the input data. It's particularly efficient when the range of input data (k) is not significantly larger than the number of elements (n).

## How Counting Sort Works

Counting sort works by counting the number of occurrences of each unique element in the input array, then using arithmetic to determine the positions of each element in the sorted output array.

### Key Characteristics:
- **Time Complexity**: O(n + k)
- **Space Complexity**: O(n + k)
- **Stable Sort**: Maintains the relative order of equal elements
- **Non-Comparison Based**: Doesn't compare elements against each other
- **Integer Sorting**: Works best with integer data within a known range

## Step-by-Step Algorithm

1. **Find the Maximum Value**: Determine the range of input values.
2. **Initialize Count Array**: Create an array to store counts of each value.
3. **Count Occurrences**: Populate the count array with frequency of each element.
4. **Compute Cumulative Counts**: Transform count array to store positions.
5. **Build Output Array**: Place elements in their correct positions.
6. **Copy Back**: Transfer sorted elements to original array.

## Detailed Implementation in C

```c
#include <stdio.h>
#include <stdlib.h>

void countingSort(int arr[], int n) {
    // Step 1: Find the maximum element to determine the range
    int max = arr[0];
    for (int i = 1; i < n; i++) {
        if (arr[i] > max) {
            max = arr[i];
        }
    }

    // Step 2: Create and initialize count array
    int *count = (int *)calloc(max + 1, sizeof(int));
    if (count == NULL) {
        printf("Memory allocation failed!\n");
        return;
    }

    // Step 3: Store the count of each element
    for (int i = 0; i < n; i++) {
        count[arr[i]]++;
    }

    // Step 4: Modify the count array to store cumulative counts
    for (int i = 1; i <= max; i++) {
        count[i] += count[i - 1];
    }

    // Step 5: Create the output array
    int *output = (int *)malloc(n * sizeof(int));
    if (output == NULL) {
        printf("Memory allocation failed!\n");
        free(count);
        return;
    }

    // Step 6: Place the elements in sorted order
    // We process the original array from end to start to maintain stability
    for (int i = n - 1; i >= 0; i--) {
        output[count[arr[i]] - 1] = arr[i];
        count[arr[i]]--;
    }

    // Step 7: Copy the sorted elements back to original array
    for (int i = 0; i < n; i++) {
        arr[i] = output[i];
    }

    // Free allocated memory
    free(count);
    free(output);
}

// Utility function to print an array
void printArray(int arr[], int n) {
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");
}

// Driver program
int main() {
    int arr[] = {4, 2, 2, 8, 3, 3, 1, 0, 5, 7, 6};
    int n = sizeof(arr) / sizeof(arr[0]);

    printf("Original array: ");
    printArray(arr, n);

    countingSort(arr, n);

    printf("Sorted array: ");
    printArray(arr, n);

    return 0;
}
```

## Detailed Explanation of Each Step

### 1. Finding the Maximum Value
```c
int max = arr[0];
for (int i = 1; i < n; i++) {
    if (arr[i] > max) {
        max = arr[i];
    }
}
```
- We scan the entire array to find the maximum value.
- This determines the size needed for our count array (0 to max).

### 2. Creating the Count Array
```c
int *count = (int *)calloc(max + 1, sizeof(int));
```
- We allocate an array of size max+1 initialized to zeros.
- `calloc` is used instead of `malloc` to ensure initialization to zero.

### 3. Counting Element Occurrences
```c
for (int i = 0; i < n; i++) {
    count[arr[i]]++;
}
```
- For each element in the input array, we increment its corresponding index in the count array.
- After this step, count[i] contains the number of elements equal to i.

### 4. Computing Cumulative Counts
```c
for (int i = 1; i <= max; i++) {
    count[i] += count[i - 1];
}
```
- We transform the count array so that each element at each index stores the sum of previous counts.
- Now count[i] contains the number of elements less than or equal to i.
- This gives us the positions where elements should be placed in the output array.

### 5. Building the Output Array
```c
for (int i = n - 1; i >= 0; i--) {
    output[count[arr[i]] - 1] = arr[i];
    count[arr[i]]--;
}
```
- We process the input array from right to left to maintain stability.
- For each element arr[i], we find its position in the output array using count[arr[i]] - 1.
- We place the element at this position and decrement the count.

### 6. Copying Back to Original Array
```c
for (int i = 0; i < n; i++) {
    arr[i] = output[i];
}
```
- Finally, we copy the sorted elements from the output array back to the original array.

## Advantages of Counting Sort

1. **Linear Time Complexity**: O(n + k) time makes it very efficient for suitable inputs.
2. **Stable Sorting**: Maintains the relative order of equal elements.
3. **Simple Implementation**: The algorithm is straightforward to implement.
4. **No Comparisons**: Avoids the Ω(n log n) lower bound of comparison sorts.

## Limitations of Counting Sort

1. **Integer Restriction**: Only works for integer data (or data that can be mapped to integers).
2. **Range Dependency**: Performance degrades when k is large compared to n.
3. **Space Requirements**: Needs additional O(n + k) space.
4. **Non-Adaptive**: Doesn't take advantage of existing order in the input.

## When to Use Counting Sort

Counting sort is ideal when:
- The range of input data (k) is not significantly larger than the number of elements (n)
- You need a stable sort
- You're sorting integer data
- Memory usage is not a primary concern

## Variations and Optimizations

1. **Negative Number Support**: Can be extended to handle negative numbers by finding both min and max values.
2. **In-Place Counting Sort**: Some variations can sort with less additional memory.
3. **Parallel Counting Sort**: Can be parallelized for large datasets.

## Example Walkthrough

Let's sort the array: [4, 2, 2, 8, 3, 3, 1]

1. Find max: 8
2. Count array (size 9): [0, 0, 0, 0, 0, 0, 0, 0, 0]
3. After counting: [0, 1, 2, 2, 1, 0, 0, 0, 1]
   (0 appears 0 times, 1 appears 1 time, etc.)
4. Cumulative counts: [0, 1, 3, 5, 6, 6, 6, 6, 7]
5. Building output (processing input from end to start):
   - Place 1 at position 0 (count[1] = 1 → position 0)
   - Place 3 at position 4 (count[3] = 5 → position 4)
   - Update count[3] to 4
   - Place 3 at position 3 (count[3] now 4 → position 3)
   - And so on...
6. Final sorted array: [1, 2, 2, 3, 3, 4, 8]

This implementation provides a complete, robust counting sort with proper memory management and clear step-by-step comments to explain each part of the algorithm.
