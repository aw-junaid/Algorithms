### **Insertion Sort, Algorithm Analysis, and Design**  

---

## **1. Insertion Sort**  
Insertion sort is a simple, **comparison-based sorting algorithm** that builds the final sorted array one element at a time. It works similarly to how you might sort playing cards in your hand.  

### **How Insertion Sort Works**  
1. **Divide the array** into a **sorted** and **unsorted** part.  
2. **Take the first element** from the unsorted part and **insert it into its correct position** in the sorted part.  
3. **Repeat** until the entire array is sorted.  

### **Pseudocode**  
```plaintext
for i = 1 to n-1:  
    key = A[i]  
    j = i - 1  
    while j >= 0 and A[j] > key:  
        A[j + 1] = A[j]  
        j = j - 1  
    A[j + 1] = key  
```

### **Example**  
**Input:** `[5, 2, 4, 6, 1, 3]`  
**Steps:**  
1. `[2, 5, 4, 6, 1, 3]` (Insert 2 before 5)  
2. `[2, 4, 5, 6, 1, 3]` (Insert 4 between 2 and 5)  
3. `[2, 4, 5, 6, 1, 3]` (6 is already in place)  
4. `[1, 2, 4, 5, 6, 3]` (Insert 1 at the beginning)  
5. `[1, 2, 3, 4, 5, 6]` (Insert 3 between 2 and 4)  

### **Time Complexity**  
- **Best Case (Already Sorted):** $\(O(n)\)$ (Only comparisons, no shifts)  
- **Average Case:** $\(O(n^2)\)$  
- **Worst Case (Reverse Sorted):** $\(O(n^2)\)$  

### **Space Complexity:** $\(O(1)\)$ (In-place sorting)  

### **When to Use Insertion Sort?**  
- Small datasets or nearly sorted arrays.  
- Stable sort (does not change the order of equal elements).  
- Simple implementation with low overhead.  

---

## **2. Analyzing Algorithms**  
Algorithm analysis helps determine **efficiency** (time & space) and **correctness**.  

### **Key Concepts**  
1. **Time Complexity:**  
   - Measures how runtime grows with input size (Big-O notation).  
   - Example: $\(O(n^2)\)$ means doubling input size quadruples runtime.  

2. **Space Complexity:**  
   - Measures extra memory used (e.g., $\(O(1)\)$ for in-place sorts).  

3. **Best, Average, and Worst Cases:**  
   - **Best Case:** Minimum time (e.g., already sorted).  
   - **Average Case:** Expected time for random inputs.  
   - **Worst Case:** Upper bound (e.g., reverse-sorted input).  

### **Example: Insertion Sort Analysis**  
- **Worst Case:** Each element must shift all previous elements → $\(O(n^2)\)$.  
- **Best Case:** Array is sorted → Only $\(O(n)\)$ comparisons.  

---

## **3. Designing Algorithms**  
Algorithm design involves creating efficient, correct procedures to solve problems. Common techniques include:  

### **Approaches to Algorithm Design**  
1. **Brute Force:**  
   - Try all possible solutions (e.g., linear search).  
   - Simple but inefficient for large inputs.  

2. **Divide and Conquer:**  
   - Break problem into subproblems (e.g., merge sort, quicksort).  
   - Recursively solve and combine results.  

3. **Greedy Algorithms:**  
   - Make locally optimal choices at each step (e.g., Dijkstra’s shortest path).  
   - May not always give the best global solution.  

4. **Dynamic Programming:**  
   - Store solutions to overlapping subproblems (e.g., Fibonacci, knapsack).  
   - Avoids redundant computations.  

5. **Backtracking:**  
   - Explore possible solutions and backtrack if invalid (e.g., N-Queens).  

### **Steps in Algorithm Design**  
1. **Understand the Problem:** Define inputs, outputs, constraints.  
2. **Choose a Strategy:** Greedy, DP, etc.  
3. **Develop Pseudocode:** Outline steps clearly.  
4. **Analyze Efficiency:** Time & space complexity.  
5. **Implement & Test:** Verify correctness and edge cases.  

### **Example: Designing a Sorting Algorithm**  
- **Problem:** Sort an array in ascending order.  
- **Approach:**  
  - **Brute Force:** Bubble sort $(\(O(n^2)\))$.  
  - **Divide & Conquer:** Merge sort $(\(O(n \log n)\))$.  
  - **Greedy:** Insertion sort (efficient for small data).  

---

### **Conclusion**  
- **Insertion Sort** is simple but inefficient for large datasets.  
- **Algorithm Analysis** helps compare efficiency using Big-O.  
- **Algorithm Design** involves structured problem-solving techniques.  

---

### **Insertion Sort in C**  
```c
#include <stdio.h>

// Function to perform insertion sort
void insertionSort(int arr[], int n) {
    int i, key, j;
    for (i = 1; i < n; i++) {
        key = arr[i]; // Current element to be inserted
        j = i - 1;

        // Shift elements greater than 'key' to the right
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key; // Insert 'key' in correct position
    }
}

// Function to print an array
void printArray(int arr[], int n) {
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");
}

int main() {
    int arr[] = {12, 11, 13, 5, 6};
    int n = sizeof(arr) / sizeof(arr[0]);

    printf("Original array: ");
    printArray(arr, n);

    insertionSort(arr, n);

    printf("Sorted array: ");
    printArray(arr, n);

    return 0;
}
```

---

### **Explanation**  
1. **Key Steps:**  
   - The outer loop (`for`) picks each element starting from index `1` (since the first element is trivially sorted).  
   - The inner loop (`while`) shifts elements greater than `key` to the right until the correct position for `key` is found.  
   - Finally, `key` is inserted in its correct position in the sorted subarray.  

2. **Example Walkthrough:**  
   - **Input:** `[12, 11, 13, 5, 6]`  
   - **Pass 1:** `[11, 12, 13, 5, 6]` (Insert 11 before 12)  
   - **Pass 2:** `[11, 12, 13, 5, 6]` (13 is already in place)  
   - **Pass 3:** `[5, 11, 12, 13, 6]` (Insert 5 at the beginning)  
   - **Pass 4:** `[5, 6, 11, 12, 13]` (Insert 6 between 5 and 11)  

3. **Time Complexity:**  
   - **Best Case:** $\(O(n)\)$ (already sorted).  
   - **Worst/Average Case:** $\(O(n^2)\)$ (nested loops).  

4. **Space Complexity:** $\(O(1)\)$ (in-place sorting, no extra memory).  

---

### **Compile & Run**  
```bash
gcc insertion_sort.c -o insertion_sort
./insertion_sort
```

**Output:**  
```
Original array: 12 11 13 5 6 
Sorted array: 5 6 11 12 13 
```

---

### **Key Points**  
- **Advantages:**  
  - Simple to implement.  
  - Efficient for small or nearly sorted datasets.  
  - Stable (does not change order of equal elements).  
- **Disadvantages:**  
  - Inefficient for large datasets (use quicksort/mergesort instead).  

