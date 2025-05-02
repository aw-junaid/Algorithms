# Bucket Sort: A Linear-Time Distribution Sorting Algorithm

Bucket sort is a distribution sorting algorithm that works by distributing elements into a number of buckets, then sorting the buckets individually, either using a different sorting algorithm or by recursively applying bucket sort. It's most effective when the input is uniformly distributed over a range.

## How Bucket Sort Works

Bucket sort operates under the assumption that the input data is uniformly distributed across a range. It partitions this range into a number of equal-sized intervals (buckets), distributes the input elements into these buckets, then sorts each bucket independently.

### Key Characteristics:
- **Average Time Complexity**: O(n + k) where k is number of buckets
- **Worst Case Complexity**: O(n²) when all elements fall into one bucket
- **Space Complexity**: O(n + k)
- **Stability**: Depends on the sorting algorithm used for buckets
- **Non-Comparison Based**: Uses distribution into buckets

## Step-by-Step Algorithm

1. **Determine Range**: Find the range of input values
2. **Create Buckets**: Initialize an array of empty buckets
3. **Scatter Elements**: Distribute elements into appropriate buckets
4. **Sort Buckets**: Sort elements in each bucket
5. **Gather Elements**: Concatenate all sorted buckets

## Detailed Implementation in C

```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

// Node structure for linked list in buckets
typedef struct Node {
    float data;
    struct Node* next;
} Node;

// Function to create a new node
Node* createNode(float value) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->data = value;
    newNode->next = NULL;
    return newNode;
}

// Insertion sort for linked list (used to sort individual buckets)
void insertionSort(Node** headRef) {
    Node* sorted = NULL;
    Node* current = *headRef;
    
    while (current != NULL) {
        Node* next = current->next;
        
        if (sorted == NULL || sorted->data >= current->data) {
            current->next = sorted;
            sorted = current;
        } else {
            Node* temp = sorted;
            while (temp->next != NULL && temp->next->data < current->data) {
                temp = temp->next;
            }
            current->next = temp->next;
            temp->next = current;
        }
        
        current = next;
    }
    
    *headRef = sorted;
}

// Bucket Sort function
void bucketSort(float arr[], int n) {
    // 1. Create n empty buckets
    Node** buckets = (Node**)malloc(n * sizeof(Node*));
    for (int i = 0; i < n; i++) {
        buckets[i] = NULL;
    }
    
    // 2. Find range of input array
    float min_val = arr[0], max_val = arr[0];
    for (int i = 1; i < n; i++) {
        if (arr[i] < min_val) {
            min_val = arr[i];
        } else if (arr[i] > max_val) {
            max_val = arr[i];
        }
    }
    float range = max_val - min_val;
    if (range == 0) return; // All elements are same
    
    // 3. Put array elements into buckets
    for (int i = 0; i < n; i++) {
        // Calculate index for bucket
        float normalized = (arr[i] - min_val) / range;
        int index = (int)(normalized * (n - 1));
        
        // Insert into bucket (using linked list)
        Node* newNode = createNode(arr[i]);
        newNode->next = buckets[index];
        buckets[index] = newNode;
    }
    
    // 4. Sort individual buckets
    for (int i = 0; i < n; i++) {
        insertionSort(&buckets[i]);
    }
    
    // 5. Concatenate all buckets into arr[]
    int index = 0;
    for (int i = 0; i < n; i++) {
        Node* current = buckets[i];
        while (current != NULL) {
            arr[index++] = current->data;
            Node* temp = current;
            current = current->next;
            free(temp); // Free node memory
        }
    }
    
    free(buckets); // Free bucket array
}

// Utility function to print array
void printArray(float arr[], int n) {
    for (int i = 0; i < n; i++) {
        printf("%.2f ", arr[i]);
    }
    printf("\n");
}

// Driver program
int main() {
    float arr[] = {0.897, 0.565, 0.656, 0.1234, 0.665, 0.3434};
    int n = sizeof(arr) / sizeof(arr[0]);
    
    printf("Original array: ");
    printArray(arr, n);
    
    bucketSort(arr, n);
    
    printf("Sorted array: ");
    printArray(arr, n);
    
    return 0;
}
```

## Detailed Explanation of Each Component

### 1. Node Structure and Creation
```c
typedef struct Node {
    float data;
    struct Node* next;
} Node;

Node* createNode(float value) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->data = value;
    newNode->next = NULL;
    return newNode;
}
```
- Defines a linked list node structure to hold bucket elements
- `createNode` function allocates memory for new nodes

### 2. Insertion Sort for Buckets
```c
void insertionSort(Node** headRef) {
    // Implementation as shown above
}
```
- Sorts each bucket using insertion sort
- Efficient for small lists (which buckets should be)
- Maintains stability by preserving original order of equal elements

### 3. Main Bucket Sort Function
```c
void bucketSort(float arr[], int n) {
    // Implementation as shown above
}
```
1. Creates n empty buckets (using linked lists)
2. Finds range of input values (min and max)
3. Distributes elements into buckets based on normalized value
4. Sorts each bucket using insertion sort
5. Concatenates all sorted buckets back into original array

### 4. Bucket Distribution Logic
```c
float normalized = (arr[i] - min_val) / range;
int index = (int)(normalized * (n - 1));
```
- Normalizes value to [0,1] range
- Maps normalized value to appropriate bucket index
- Ensures uniform distribution across buckets

## Advantages of Bucket Sort

1. **Linear Average Time**: O(n) when input is uniformly distributed
2. **Stable Sorting**: When using stable sorting for buckets
3. **Effective for Uniform Data**: Ideal for uniformly distributed floats
4. **Parallelizable**: Buckets can be processed independently

## Limitations of Bucket Sort

1. **Non-Uniform Data Performance**: Degrades to O(n²) for clustered data
2. **Extra Space**: Requires O(n + k) additional space
3. **Input Range Knowledge**: Needs to know data range in advance
4. **Float/Integer Restriction**: Primarily for numeric data types

## When to Use Bucket Sort

Bucket sort is ideal when:
- Input is uniformly distributed over a known range
- Sorting floating-point numbers in [0,1) range
- Additional memory is available
- Stable sorting is required

## Example Walkthrough

Sorting: [0.897, 0.565, 0.656, 0.1234, 0.665, 0.3434] with 6 buckets

1. **Range**: min = 0.1234, max = 0.897 → range = 0.7736
2. **Bucket Distribution**:
   - Bucket 0 (0.000-0.166): 0.1234
   - Bucket 2 (0.333-0.500): 0.3434
   - Bucket 4 (0.666-0.833): 0.665, 0.656, 0.897
   - Bucket 3 (0.500-0.666): 0.565
3. **Sort Buckets**:
   - Bucket 0: [0.1234]
   - Bucket 2: [0.3434]
   - Bucket 3: [0.565]
   - Bucket 4: [0.656, 0.665, 0.897]
4. **Concatenate**: [0.1234, 0.3434, 0.565, 0.656, 0.665, 0.897]

## Optimizations and Variations

1. **Dynamic Bucket Count**: Adjust number of buckets based on input size
2. **Alternative Sorting Algorithms**: Use quicksort for larger buckets
3. **Hybrid Approaches**: Combine with other sorting algorithms
4. **Parallel Processing**: Sort buckets in parallel threads

This implementation provides a complete bucket sort solution for floating-point numbers, including all necessary components with detailed comments explaining each step. The code handles memory allocation and deallocation properly and demonstrates the key concepts of bucket sort.
