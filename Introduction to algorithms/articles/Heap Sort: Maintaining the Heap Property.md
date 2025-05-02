# Heap Sort: Maintaining the Heap Property - Detailed Explanation and C Implementation

Heap sort relies heavily on maintaining the heap property throughout the sorting process. This detailed explanation will focus on how heap sort maintains the heap property, followed by a complete C implementation.

## Understanding Heap Property Maintenance

### 1. What is the Heap Property?

The heap property is the fundamental characteristic that defines a heap data structure:
- **Max-Heap Property**: For any node, its value is ≥ the values of its children
- **Min-Heap Property**: For any node, its value is ≤ the values of its children

Heap sort typically uses a max-heap for sorting in ascending order.

### 2. Why Maintain the Heap Property?

Maintaining the heap property ensures that:
- The largest element is always at the root (for max-heap)
- We can efficiently extract the maximum element
- The structure remains a valid heap after each extraction

### 3. How Heap Sort Maintains the Property

Heap sort maintains the heap property through two key operations:
1. **Heapify (or sift-down)**: Restores the heap property when it might be violated at a specific node
2. **Build-Heap**: Constructs an initial heap from an unsorted array

## Detailed Process of Maintaining Heap Property

### 1. Heapify Operation (Max-Heap)

The heapify operation ensures the max-heap property is maintained for a subtree rooted at index `i`:

1. Compare the node with its left and right children
2. If either child is larger, swap with the largest child
3. Recursively heapify the affected subtree
4. Continue until the node is ≥ its children or becomes a leaf

**Time Complexity**: O(log n) - proportional to the height of the tree

### 2. Build-Heap Operation

This converts an arbitrary array into a valid max-heap:

1. Start from the last non-leaf node (index n/2 - 1)
2. Heapify each node moving up to the root
3. After processing all nodes, the array satisfies max-heap property

**Time Complexity**: O(n) - tighter analysis shows it's linear time

### 3. Sorting Phase

After building the heap:
1. Swap root (max element) with last element
2. Reduce heap size by 1 (excluding sorted elements)
3. Heapify the new root to restore heap property
4. Repeat until heap is empty

## Complete C Implementation

Here's the complete implementation with detailed comments:

```c
#include <stdio.h>

// Utility function to swap two elements
void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

/* 
 * Maintains the max-heap property for a subtree rooted at index i
 * n: size of the heap
 * i: index of the root node to heapify
 */
void maxHeapify(int arr[], int n, int i) {
    int largest = i;        // Initialize largest as root
    int left = 2 * i + 1;   // Left child index
    int right = 2 * i + 2;  // Right child index

    // If left child exists and is greater than root
    if (left < n && arr[left] > arr[largest])
        largest = left;

    // If right child exists and is greater than current largest
    if (right < n && arr[right] > arr[largest])
        largest = right;

    // If largest is not root, swap and continue heapifying
    if (largest != i) {
        swap(&arr[i], &arr[largest]);
        maxHeapify(arr, n, largest);  // Recursively heapify the affected subtree
    }
}

/*
 * Builds a max-heap from an unsorted array
 * This ensures the heap property is satisfied for the entire array
 */
void buildMaxHeap(int arr[], int n) {
    // Start from the last non-leaf node and work up to the root
    for (int i = n / 2 - 1; i >= 0; i--) {
        maxHeapify(arr, n, i);
    }
}

/*
 * Main heap sort function
 * 1. Builds max-heap
 * 2. Repeatedly extracts max element and maintains heap property
 */
void heapSort(int arr[], int n) {
    // First build a max heap
    buildMaxHeap(arr, n);

    // One by one extract elements from heap
    for (int i = n - 1; i > 0; i--) {
        // Move current root (max) to end
        swap(&arr[0], &arr[i]);
        
        // Call maxHeapify on the reduced heap (size = i)
        maxHeapify(arr, i, 0);
    }
}

// Utility function to print an array
void printArray(int arr[], int n) {
    for (int i = 0; i < n; ++i)
        printf("%d ", arr[i]);
    printf("\n");
}

// Driver program
int main() {
    int arr[] = {4, 10, 3, 5, 1, 8, 7, 2, 9, 6};
    int n = sizeof(arr) / sizeof(arr[0]);

    printf("Original array:\n");
    printArray(arr, n);

    heapSort(arr, n);

    printf("Sorted array:\n");
    printArray(arr, n);

    return 0;
}
```

## Step-by-Step Explanation with Example

Let's trace the algorithm with the array `[4, 10, 3, 5, 1, 8, 7, 2, 9, 6]`

### 1. Build Max-Heap Phase

1. Start from last non-leaf node (index 4, value 1)
2. Heapify index 4: No change (already a leaf)
3. Heapify index 3 (5): No change (children 2=9, 2*3+1=10 is out of bounds)
4. Heapify index 2 (3):
   - Compare with left (8) and right (7)
   - Swap with 8 → [4,10,8,5,1,3,7,2,9,6]
5. Heapify index 1 (10):
   - Compare with left (5) and right (1)
   - 10 is largest, no swap
6. Heapify index 0 (4):
   - Compare with left (10) and right (8)
   - Swap with 10 → [10,4,8,5,1,3,7,2,9,6]
   - Now heapify index 1 (4):
     - Compare with left (9) and right (6)
     - Swap with 9 → [10,9,8,5,1,3,7,2,4,6]

Final max-heap: `[10, 9, 8, 5, 1, 3, 7, 2, 4, 6]`

### 2. Sorting Phase

1. Swap root (10) with last element (6):
   - Array: [6,9,8,5,1,3,7,2,4,10]
   - Heap size reduces to 9
   - Heapify root (6):
     - Swap with 9 → [9,6,8,5,1,3,7,2,4,10]
     - Heapify index 1 (6):
       - Swap with 5 → [9,5,8,6,1,3,7,2,4,10]
2. Repeat process until sorted

Final sorted array: `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`

## Key Observations

1. **Heapify Direction**: The heapify operation works downward (sift-down), comparing parent with children and moving larger values up

2. **Efficiency**: Each heapify operation takes O(log n) time, and we perform it O(n) times, resulting in O(n log n) overall complexity

3. **In-Place**: The algorithm uses constant extra space (O(1)), making it memory efficient

4. **Not Stable**: Heap sort doesn't maintain the relative order of equal elements

This implementation demonstrates how heap sort maintains the heap property throughout the sorting process, ensuring efficient extraction of maximum elements while keeping the data structure valid.
