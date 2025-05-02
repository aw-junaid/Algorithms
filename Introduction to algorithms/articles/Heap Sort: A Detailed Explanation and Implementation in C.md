# Heap Sort: A Detailed Explanation and Implementation in C

Heap sort is a comparison-based sorting algorithm that uses a binary heap data structure to sort elements. It has an O(n log n) time complexity, making it efficient for large datasets. Let's break down everything about heap sort.

## Understanding Heap Sort

### 1. What is a Heap?
A heap is a specialized tree-based data structure that satisfies the heap property:
- **Max-Heap**: For any given node, its value is greater than or equal to its children's values
- **Min-Heap**: For any given node, its value is less than or equal to its children's values

Heap sort typically uses a max-heap for sorting in ascending order.

### 2. Heap Sort Process
Heap sort works in two main phases:
1. **Build Heap**: Transform the input array into a max-heap
2. **Sorting**:
   - Swap the root (maximum element) with the last element
   - Reduce heap size by 1 (excluding the sorted elements)
   - Heapify the root to maintain the max-heap property
   - Repeat until the heap is empty

### 3. Time Complexity
- **Best case**: O(n log n)
- **Average case**: O(n log n)
- **Worst case**: O(n log n)
- **Space complexity**: O(1) (in-place sorting)

### 4. Advantages
- Efficient O(n log n) time complexity
- In-place sorting (requires no additional storage)
- Not recursive (avoids recursion overhead)

### 5. Disadvantages
- Not stable (doesn't maintain relative order of equal elements)
- Not as fast as quicksort in practice for most cases
- Poor cache performance compared to other algorithms

## Heap Sort Implementation in C

Here's a complete implementation of heap sort in C:

```c
#include <stdio.h>

// Function to swap two elements
void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

// Heapify a subtree rooted with node i (an index in arr[])
// n is the size of the heap
void heapify(int arr[], int n, int i) {
    int largest = i;       // Initialize largest as root
    int left = 2 * i + 1;  // left child
    int right = 2 * i + 2; // right child

    // If left child is larger than root
    if (left < n && arr[left] > arr[largest])
        largest = left;

    // If right child is larger than largest so far
    if (right < n && arr[right] > arr[largest])
        largest = right;

    // If largest is not root
    if (largest != i) {
        swap(&arr[i], &arr[largest]);
        // Recursively heapify the affected sub-tree
        heapify(arr, n, largest);
    }
}

// Main function to do heap sort
void heapSort(int arr[], int n) {
    // Build heap (rearrange array)
    for (int i = n / 2 - 1; i >= 0; i--)
        heapify(arr, n, i);

    // One by one extract an element from heap
    for (int i = n - 1; i > 0; i--) {
        // Move current root to end
        swap(&arr[0], &arr[i]);
        // call max heapify on the reduced heap
        heapify(arr, i, 0);
    }
}

// Function to print an array
void printArray(int arr[], int n) {
    for (int i = 0; i < n; ++i)
        printf("%d ", arr[i]);
    printf("\n");
}

// Driver code
int main() {
    int arr[] = {12, 11, 13, 5, 6, 7};
    int n = sizeof(arr) / sizeof(arr[0]);

    printf("Original array: \n");
    printArray(arr, n);

    heapSort(arr, n);

    printf("Sorted array: \n");
    printArray(arr, n);
    
    return 0;
}
```

## Code Explanation

1. **swap()**: A helper function to swap two integer values.

2. **heapify()**: 
   - Maintains the max-heap property for a subtree rooted at index 'i'
   - Compares the root with its left and right children
   - If a child is larger, swaps them and recursively heapifies the affected subtree

3. **heapSort()**:
   - First builds a max-heap from the input array (starting from the last non-leaf node)
   - Repeatedly extracts the maximum element (root) and places it at the end of the array
   - Reduces the heap size and heapifies the root

4. **printArray()**: Utility function to print the array.

5. **main()**: 
   - Demonstrates the usage with a sample array
   - Shows the array before and after sorting

## Visualizing Heap Sort

Let's visualize the sorting process with the sample array [12, 11, 13, 5, 6, 7]:

1. **Build Heap Phase**:
   - Start from the last non-leaf node (index 2, value 13)
   - Heapify moves up to index 1 (value 11), then index 0 (value 12)
   - Final max-heap: [13, 11, 12, 5, 6, 7]

2. **Sorting Phase**:
   - Swap root (13) with last element (7), then heapify [7, 11, 12, 5, 6]
   - Next swap root (12) with last element (6), heapify [6, 11, 7, 5]
   - Continue this process until the array is sorted

The final sorted array will be [5, 6, 7, 11, 12, 13].

Heap sort is a powerful algorithm that combines the best of insertion sort (in-place) and merge sort (O(n log n) time), making it useful for systems where memory is constrained.
