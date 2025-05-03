# Amortized Analysis: A Detailed Explanation with C Implementations

Amortized analysis is a technique for analyzing the average time complexity of operations in a sequence, where some operations may be expensive but are balanced by many cheaper operations. Let's explore each method in detail with C implementations.

## 1. Aggregate Analysis

**Concept**: In aggregate analysis, we determine an upper bound T(n) on the total cost of a sequence of n operations, then calculate the average cost per operation as T(n)/n.

**Example**: Dynamic array (similar to std::vector in C++) where we double the size when full.

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    int *array;
    size_t size;
    size_t capacity;
} DynamicArray;

void initArray(DynamicArray *da, size_t initialCapacity) {
    da->array = (int *)malloc(initialCapacity * sizeof(int));
    da->size = 0;
    da->capacity = initialCapacity;
}

void pushBack(DynamicArray *da, int value) {
    if (da->size == da->capacity) {
        // Double the capacity when full (expensive operation)
        da->capacity *= 2;
        da->array = (int *)realloc(da->array, da->capacity * sizeof(int));
        printf("Resized to %zu\n", da->capacity);
    }
    da->array[da->size++] = value;
}

void freeArray(DynamicArray *da) {
    free(da->array);
    da->array = NULL;
    da->size = da->capacity = 0;
}

int main() {
    DynamicArray da;
    initArray(&da, 2); // Start with capacity 2
    
    for (int i = 0; i < 10; i++) {
        pushBack(&da, i);
        printf("Added %d, size=%zu, capacity=%zu\n", i, da.size, da.capacity);
    }
    
    freeArray(&da);
    return 0;
}
```

**Analysis**: 
- The cost of n push operations is O(n) because the total cost T(n) of n operations is at most n (for insertions) plus the cost of doublings (1 + 2 + 4 + ... + 2^⌊log₂n⌋) = O(n).
- Thus, the amortized cost per operation is O(n)/n = O(1).

## 2. The Accounting Method

**Concept**: We assign different charges to operations (amortized costs). Some operations are charged more than their actual cost, with the excess stored as credit to pay for future expensive operations.

**Example**: Dynamic array using accounting method.

```c
// Same DynamicArray structure as before

void pushBackAccounting(DynamicArray *da, int value, int *credit) {
    int actualCost = 1; // Basic insertion cost
    int amortizedCost = 3; // We choose this based on analysis
    
    if (da->size == da->capacity) {
        // Resizing costs 'size' (copying all elements)
        actualCost += da->size;
    }
    
    // Use credit to pay for actual cost
    *credit += amortizedCost - actualCost;
    
    // Perform the operation
    if (da->size == da->capacity) {
        da->capacity *= 2;
        da->array = (int *)realloc(da->array, da->capacity * sizeof(int));
    }
    da->array[da->size++] = value;
    
    printf("Added %d, size=%zu, capacity=%zu, credit=%d\n", 
           value, da.size, da.capacity, *credit);
}

int main() {
    DynamicArray da;
    initArray(&da, 2);
    int credit = 0;
    
    for (int i = 0; i < 10; i++) {
        pushBackAccounting(&da, i, &credit);
    }
    
    freeArray(&da);
    return 0;
}
```

**Analysis**: 
- We assign an amortized cost of 3 per insertion.
- For non-expanding insertions: actual cost=1, credit=2
- For expanding insertions: actual cost=1 + n (copying), but we've saved 2*(n/2)=n credit from previous insertions
- Credit never goes negative, proving O(1) amortized cost.

## 3. The Potential Method

**Concept**: We define a potential function Φ on the data structure's state. The amortized cost is actual cost plus change in potential.

**Example**: Dynamic array with potential function Φ = 2*size - capacity

```c
// Same DynamicArray structure

void pushBackPotential(DynamicArray *da, int value, int *totalAmortized) {
    int actualCost = 1;
    int oldPotential = 2 * da->size - da->capacity;
    
    if (da->size == da->capacity) {
        actualCost += da->size; // Copy cost
    }
    
    // Perform the operation
    if (da->size == da->capacity) {
        da->capacity *= 2;
        da->array = (int *)realloc(da->array, da->capacity * sizeof(int));
    }
    da->array[da->size++] = value;
    
    int newPotential = 2 * da->size - da->capacity;
    int amortizedCost = actualCost + (newPotential - oldPotential);
    *totalAmortized += amortizedCost;
    
    printf("Added %d, size=%zu, capacity=%zu, amortized=%d, total=%d\n", 
           value, da.size, da.capacity, amortizedCost, *totalAmortized);
}

int main() {
    DynamicArray da;
    initArray(&da, 2);
    int totalAmortized = 0;
    
    for (int i = 0; i < 10; i++) {
        pushBackPotential(&da, i, &totalAmortized);
    }
    
    freeArray(&da);
    return 0;
}
```

**Analysis**:
- Φ = 2*size - capacity
- For non-expanding insertions: ΔΦ = 2, amortized cost = 1 + 2 = 3
- For expanding insertions (size = capacity = n):
  - Actual cost = 1 + n
  - ΔΦ = 2(n+1) - 2n - (2n - n) = 2 - n
  - Amortized cost = (1 + n) + (2 - n) = 3
- Thus, amortized cost is always 3 (O(1))

## 4. Dynamic Tables

**Concept**: Dynamic tables (like our dynamic array example) grow and shrink dynamically to maintain load factor. The key is to not just grow but also shrink the table when it becomes too empty, while still maintaining amortized O(1) operations.

**Implementation with expansion and contraction**:

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    int *table;
    size_t size;
    size_t capacity;
} DynamicTable;

void initTable(DynamicTable *dt, size_t initialCapacity) {
    dt->table = (int *)malloc(initialCapacity * sizeof(int));
    dt->size = 0;
    dt->capacity = initialCapacity;
}

void insert(DynamicTable *dt, int value) {
    if (dt->size == dt->capacity) {
        // Double the capacity when full
        dt->capacity *= 2;
        dt->table = (int *)realloc(dt->table, dt->capacity * sizeof(int));
        printf("Expanded to %zu\n", dt->capacity);
    }
    dt->table[dt->size++] = value;
}

void deleteLast(DynamicTable *dt) {
    if (dt->size == 0) return;
    
    dt->size--;
    
    // Contract to half when quarter full
    if (dt->size > 0 && dt->size == dt->capacity / 4) {
        dt->capacity /= 2;
        dt->table = (int *)realloc(dt->table, dt->capacity * sizeof(int));
        printf("Contracted to %zu\n", dt->capacity);
    }
}

void freeTable(DynamicTable *dt) {
    free(dt->table);
    dt->table = NULL;
    dt->size = dt->capacity = 0;
}

int main() {
    DynamicTable dt;
    initTable(&dt, 2);
    
    // Insert elements
    for (int i = 0; i < 10; i++) {
        insert(&dt, i);
        printf("Inserted %d, size=%zu, capacity=%zu\n", i, dt.size, dt.capacity);
    }
    
    // Delete elements
    while (dt.size > 0) {
        deleteLast(&dt);
        printf("Deleted, size=%zu, capacity=%zu\n", dt.size, dt.capacity);
    }
    
    freeTable(&dt);
    return 0;
}
```

**Analysis**:
- Expansion: When table is full (size = capacity), double the size
- Contraction: When table is quarter full (size = capacity/4), halve the size
- This ensures the load factor is always between 1/4 and 1
- Amortized cost is O(1) per operation using any of the three methods

## Comparison of Methods

1. **Aggregate**: Simple but less precise for complex data structures
2. **Accounting**: More flexible, allows different operations to have different amortized costs
3. **Potential**: Most powerful, can handle more complex scenarios, mathematically rigorous

All three methods should give the same amortized cost for the same problem, but some may be easier to apply than others depending on the situation.

Dynamic tables are a fundamental example where amortized analysis shows that even with occasional expensive operations, the average cost per operation can be constant.
