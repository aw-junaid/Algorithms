# Huffman Codes: A Greedy Algorithm for Data Compression

## Introduction to Huffman Coding

Huffman coding is a lossless data compression algorithm that uses variable-length codes to represent characters. It assigns shorter codes to more frequent characters and longer codes to less frequent ones, resulting in optimal prefix codes that minimize the expected code length.

## Key Concepts

### 1. Prefix Codes
- A code where no codeword is a prefix of any other codeword
- Enables unambiguous decoding without special markers
- Example: If 'a'=0, then no other code can start with 0

### 2. Optimal Code
- Minimizes the expected length of the encoded message
- Given by: L = Σ (frequency of character × length of its code)

## Huffman's Greedy Algorithm

### Algorithm Steps:

1. **Frequency Calculation**:
   - Calculate the frequency of each character in the input

2. **Priority Queue Construction**:
   - Create a min-heap (priority queue) where each node represents a character and its frequency

3. **Tree Construction**:
   - While there's more than one node in the queue:
     a. Extract the two nodes with the smallest frequencies
     b. Create a new internal node with frequency = sum of the two nodes' frequencies
     c. Make the first extracted node as left child and the second as right child
     d. Add the new node back to the queue

4. **Code Assignment**:
   - Traverse the tree from root to leaves
   - Assign '0' for left edges and '1' for right edges
   - The path from root to leaf gives the Huffman code for that character

## Why It's Greedy

Huffman coding exhibits the two key properties of greedy algorithms:
1. **Greedy Choice Property**: At each step, we merge the two least frequent nodes
2. **Optimal Substructure**: The optimal solution contains optimal solutions to subproblems (subtrees)

## Time Complexity

- Building the priority queue: O(n)
- Extracting min and inserting: O(log n) per operation
- Total for n characters: O(n log n)

## C Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_TREE_HT 100

// Huffman tree node
struct MinHeapNode {
    char data;
    unsigned freq;
    struct MinHeapNode *left, *right;
};

// Min-heap structure
struct MinHeap {
    unsigned size;
    unsigned capacity;
    struct MinHeapNode** array;
};

// Create a new min heap node
struct MinHeapNode* newNode(char data, unsigned freq) {
    struct MinHeapNode* temp = (struct MinHeapNode*)malloc(sizeof(struct MinHeapNode));
    temp->left = temp->right = NULL;
    temp->data = data;
    temp->freq = freq;
    return temp;
}

// Create a min heap
struct MinHeap* createMinHeap(unsigned capacity) {
    struct MinHeap* minHeap = (struct MinHeap*)malloc(sizeof(struct MinHeap));
    minHeap->size = 0;
    minHeap->capacity = capacity;
    minHeap->array = (struct MinHeapNode**)malloc(minHeap->capacity * sizeof(struct MinHeapNode*));
    return minHeap;
}

// Swap two min heap nodes
void swapMinHeapNode(struct MinHeapNode** a, struct MinHeapNode** b) {
    struct MinHeapNode* t = *a;
    *a = *b;
    *b = t;
}

// Heapify function
void minHeapify(struct MinHeap* minHeap, int idx) {
    int smallest = idx;
    int left = 2 * idx + 1;
    int right = 2 * idx + 2;

    if (left < minHeap->size && 
        minHeap->array[left]->freq < minHeap->array[smallest]->freq)
        smallest = left;

    if (right < minHeap->size && 
        minHeap->array[right]->freq < minHeap->array[smallest]->freq)
        smallest = right;

    if (smallest != idx) {
        swapMinHeapNode(&minHeap->array[smallest], &minHeap->array[idx]);
        minHeapify(minHeap, smallest);
    }
}

// Check if size is 1
int isSizeOne(struct MinHeap* minHeap) {
    return (minHeap->size == 1);
}

// Extract minimum node
struct MinHeapNode* extractMin(struct MinHeap* minHeap) {
    struct MinHeapNode* temp = minHeap->array[0];
    minHeap->array[0] = minHeap->array[minHeap->size - 1];
    --minHeap->size;
    minHeapify(minHeap, 0);
    return temp;
}

// Insert new node
void insertMinHeap(struct MinHeap* minHeap, struct MinHeapNode* minHeapNode) {
    ++minHeap->size;
    int i = minHeap->size - 1;
    while (i && minHeapNode->freq < minHeap->array[(i - 1) / 2]->freq) {
        minHeap->array[i] = minHeap->array[(i - 1) / 2];
        i = (i - 1) / 2;
    }
    minHeap->array[i] = minHeapNode;
}

// Build min heap
void buildMinHeap(struct MinHeap* minHeap) {
    int n = minHeap->size - 1;
    for (int i = (n - 1) / 2; i >= 0; --i)
        minHeapify(minHeap, i);
}

// Print array
void printArr(int arr[], int n) {
    for (int i = 0; i < n; ++i)
        printf("%d", arr[i]);
    printf("\n");
}

// Check if node is leaf
int isLeaf(struct MinHeapNode* root) {
    return !(root->left) && !(root->right);
}

// Create and build min heap
struct MinHeap* createAndBuildMinHeap(char data[], int freq[], int size) {
    struct MinHeap* minHeap = createMinHeap(size);
    for (int i = 0; i < size; ++i)
        minHeap->array[i] = newNode(data[i], freq[i]);
    minHeap->size = size;
    buildMinHeap(minHeap);
    return minHeap;
}

// Build Huffman tree
struct MinHeapNode* buildHuffmanTree(char data[], int freq[], int size) {
    struct MinHeapNode *left, *right, *top;
    struct MinHeap* minHeap = createAndBuildMinHeap(data, freq, size);

    while (!isSizeOne(minHeap)) {
        left = extractMin(minHeap);
        right = extractMin(minHeap);
        top = newNode('$', left->freq + right->freq);
        top->left = left;
        top->right = right;
        insertMinHeap(minHeap, top);
    }
    return extractMin(minHeap);
}

// Print Huffman codes
void printCodes(struct MinHeapNode* root, int arr[], int top) {
    if (root->left) {
        arr[top] = 0;
        printCodes(root->left, arr, top + 1);
    }
    if (root->right) {
        arr[top] = 1;
        printCodes(root->right, arr, top + 1);
    }
    if (isLeaf(root)) {
        printf("%c: ", root->data);
        printArr(arr, top);
    }
}

// Huffman coding function
void HuffmanCodes(char data[], int freq[], int size) {
    struct MinHeapNode* root = buildHuffmanTree(data, freq, size);
    int arr[MAX_TREE_HT], top = 0;
    printCodes(root, arr, top);
}

int main() {
    char arr[] = { 'a', 'b', 'c', 'd', 'e', 'f' };
    int freq[] = { 5, 9, 12, 13, 16, 45 };
    int size = sizeof(arr) / sizeof(arr[0]);
    printf("Huffman Codes:\n");
    HuffmanCodes(arr, freq, size);
    return 0;
}
```

## Example Output

For the input:
- Characters: a, b, c, d, e, f
- Frequencies: 5, 9, 12, 13, 16, 45

The output might look like:
```
Huffman Codes:
f: 0
c: 100
d: 101
a: 1100
b: 1101
e: 111
```

## Applications of Huffman Coding

1. **File Compression**: Used in ZIP, GZIP, and other compression formats
2. **Image Compression**: Part of JPEG and PNG formats
3. **Network Protocols**: For efficient data transmission
4. **Database Systems**: For compact storage of data

## Advantages

1. **Optimal prefix codes**: Guarantees minimum expected code length
2. **Efficient**: O(n log n) time complexity
3. **Simple decoding**: No ambiguity due to prefix property
4. **Adaptable**: Can be modified for adaptive Huffman coding

## Limitations

1. **Two-pass algorithm**: Requires frequency statistics before encoding
2. **Not optimal for small alphabets**: Fixed-length codes may be better
3. **Sensitive to frequency changes**: Requires rebuilding the tree if frequencies change
4. **Not as efficient as arithmetic coding** for some distributions

Huffman coding remains one of the most elegant applications of greedy algorithms, demonstrating how locally optimal choices at each step can lead to a globally optimal solution.
