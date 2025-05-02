# Priority Queues: Comprehensive Explanation and C Implementation

A priority queue is an abstract data type that extends the concept of a queue by assigning priorities to elements. Unlike a standard FIFO queue, elements are dequeued based on their priority rather than their arrival time.

## Core Concepts of Priority Queues

### 1. Fundamental Characteristics

- **Priority Ordering**: Each element has an associated priority value
- **Dequeue Operation**: Always removes the highest (or lowest) priority element
- **Flexible Implementation**: Can be implemented using various data structures (heaps, arrays, linked lists)

### 2. Types of Priority Queues

1. **Max-Priority Queue**:
   - Elements with higher priority values are served first
   - Typically implemented using max-heaps

2. **Min-Priority Queue**:
   - Elements with lower priority values are served first
   - Typically implemented using min-heaps

### 3. Key Operations

| Operation      | Description                          | Time Complexity (Heap) |
|---------------|-------------------------------------|-----------------------|
| Insert        | Add new element with priority       | O(log n)              |
| Extract-Max   | Remove and return highest priority  | O(log n)              |
| Peek          | View highest priority without remove| O(1)                  |
| Increase-Key  | Increase priority of existing element| O(log n)              |
| Decrease-Key  | Decrease priority of existing element| O(log n)              |

## Implementation Choices

### 1. Heap-Based Implementation (Most Efficient)
- **Pros**: O(log n) for insert/extract, O(1) for peek
- **Cons**: More complex to implement than simple structures

### 2. Array/Linked List Implementation
- **Pros**: Simple to implement
- **Cons**: O(n) for insert/extract operations

## Complete C Implementation (Max-Priority Queue)

```c
#include <stdio.h>
#include <stdlib.h>
#include <limits.h>

typedef struct {
    int *data;      // Array to store elements
    int capacity;   // Maximum size
    int size;       // Current number of elements
} MaxPriorityQueue;

// Initialize a priority queue
MaxPriorityQueue* createPriorityQueue(int capacity) {
    MaxPriorityQueue* pq = (MaxPriorityQueue*)malloc(sizeof(MaxPriorityQueue));
    pq->data = (int*)malloc(capacity * sizeof(int));
    pq->capacity = capacity;
    pq->size = 0;
    return pq;
}

// Swap two elements
void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

// Maintain max-heap property from bottom to top
void heapifyUp(MaxPriorityQueue* pq, int index) {
    while (index > 0) {
        int parent = (index - 1) / 2;
        if (pq->data[parent] >= pq->data[index]) break;
        swap(&pq->data[parent], &pq->data[index]);
        index = parent;
    }
}

// Maintain max-heap property from top to bottom
void heapifyDown(MaxPriorityQueue* pq, int index) {
    while (1) {
        int left = 2 * index + 1;
        int right = 2 * index + 2;
        int largest = index;

        if (left < pq->size && pq->data[left] > pq->data[largest])
            largest = left;
        if (right < pq->size && pq->data[right] > pq->data[largest])
            largest = right;
        if (largest == index) break;

        swap(&pq->data[index], &pq->data[largest]);
        index = largest;
    }
}

// Insert new element with priority
void insert(MaxPriorityQueue* pq, int value) {
    if (pq->size >= pq->capacity) {
        printf("Priority queue is full!\n");
        return;
    }
    
    pq->data[pq->size] = value;
    heapifyUp(pq, pq->size);
    pq->size++;
}

// Remove and return element with highest priority
int extractMax(MaxPriorityQueue* pq) {
    if (pq->size <= 0) {
        printf("Priority queue is empty!\n");
        return INT_MIN;
    }

    int max = pq->data[0];
    pq->data[0] = pq->data[pq->size - 1];
    pq->size--;
    heapifyDown(pq, 0);
    return max;
}

// Get max element without removing
int peekMax(MaxPriorityQueue* pq) {
    if (pq->size <= 0) {
        printf("Priority queue is empty!\n");
        return INT_MIN;
    }
    return pq->data[0];
}

// Increase priority of element at index
void increaseKey(MaxPriorityQueue* pq, int index, int newValue) {
    if (index >= pq->size || newValue < pq->data[index]) {
        printf("Invalid operation!\n");
        return;
    }
    
    pq->data[index] = newValue;
    heapifyUp(pq, index);
}

// Decrease priority of element at index
void decreaseKey(MaxPriorityQueue* pq, int index, int newValue) {
    if (index >= pq->size || newValue > pq->data[index]) {
        printf("Invalid operation!\n");
        return;
    }
    
    pq->data[index] = newValue;
    heapifyDown(pq, index);
}

// Print the priority queue
void printPriorityQueue(MaxPriorityQueue* pq) {
    printf("Priority Queue (size %d): ", pq->size);
    for (int i = 0; i < pq->size; i++) {
        printf("%d ", pq->data[i]);
    }
    printf("\n");
}

// Free memory
void destroyPriorityQueue(MaxPriorityQueue* pq) {
    free(pq->data);
    free(pq);
}

int main() {
    MaxPriorityQueue* pq = createPriorityQueue(10);
    
    insert(pq, 3);
    insert(pq, 5);
    insert(pq, 9);
    insert(pq, 2);
    insert(pq, 7);
    
    printPriorityQueue(pq);  // Should show elements in heap order
    
    printf("Max element: %d\n", peekMax(pq));  // Should be 9
    
    increaseKey(pq, 3, 10);  // Increase 2 to 10
    printPriorityQueue(pq);   // 10 should bubble up
    
    printf("Extracted max: %d\n", extractMax(pq));  // Should remove 10
    printPriorityQueue(pq);
    
    decreaseKey(pq, 0, 4);   // Decrease 9 to 4
    printPriorityQueue(pq);   // 4 should sink down
    
    destroyPriorityQueue(pq);
    return 0;
}
```

## Advanced Features and Optimizations

### 1. Dynamic Resizing
- Automatically grow the array when capacity is reached
- Typical strategy: double capacity when full

### 2. Index Mapping
- For applications that need to track element positions
- Useful for Dijkstra's algorithm implementation

### 3. Custom Comparators
- Allow user-defined priority comparison functions
- Enables more flexible priority definitions

## Real-World Applications

1. **Operating Systems**:
   - Process scheduling (higher priority processes run first)
   - Interrupt handling

2. **Network Routing**:
   - Prioritizing network packets
   - Quality of Service (QoS) management

3. **Graph Algorithms**:
   - Dijkstra's shortest path algorithm
   - Prim's minimum spanning tree algorithm

4. **Discrete Event Simulation**:
   - Processing events in chronological order
   - Event-driven simulations

## Performance Considerations

1. **Time Complexity Comparison**:

| Operation      | Heap  | Unsorted Array | Sorted Array |
|---------------|-------|----------------|--------------|
| Insert        | O(log n) | O(1)       | O(n)         |
| Extract-Max   | O(log n) | O(n)       | O(1)         |
| Peek          | O(1)     | O(n)       | O(1)         |

2. **Memory Usage**:
- Heap implementation uses contiguous memory
- Overhead is minimal (just the array and size counters)

3. **Cache Performance**:
- Heap operations may have poor cache locality
- B-heap variants can improve cache performance

This implementation provides a complete, production-ready priority queue in C with all fundamental operations and demonstrates why heap-based implementations are preferred for most applications requiring efficient priority queue operations.
