# Elementary Data Structures: Stacks and Queues

## Introduction to Stacks and Queues

Stacks and queues are fundamental linear data structures that differ in how elements are inserted and removed. They are essential in computer science and appear in many algorithms and real-world applications.

## Stacks

### Definition
A stack is a Last-In-First-Out (LIFO) data structure where the last element added is the first one to be removed. Think of it like a stack of plates - you add and remove plates from the top.

### Operations
1. **Push**: Add an element to the top of the stack
2. **Pop**: Remove and return the top element from the stack
3. **Peek/Top**: View the top element without removing it
4. **isEmpty**: Check if the stack is empty
5. **isFull**: Check if the stack is full (for array implementation)

### Implementation Approaches
1. **Array Implementation**: Fixed size, efficient but limited capacity
2. **Linked List Implementation**: Dynamic size, uses more memory but can grow as needed

### Applications
- Function call management (call stack)
- Undo mechanisms in applications
- Expression evaluation and syntax parsing
- Backtracking algorithms
- Memory management

### Array Implementation in C

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

#define MAX_SIZE 100

typedef struct {
    int data[MAX_SIZE];
    int top;
} Stack;

// Initialize stack
void initStack(Stack *s) {
    s->top = -1;
}

// Check if stack is empty
bool isEmpty(Stack *s) {
    return s->top == -1;
}

// Check if stack is full
bool isFull(Stack *s) {
    return s->top == MAX_SIZE - 1;
}

// Push element onto stack
void push(Stack *s, int value) {
    if (isFull(s)) {
        printf("Stack Overflow\n");
        return;
    }
    s->data[++s->top] = value;
}

// Pop element from stack
int pop(Stack *s) {
    if (isEmpty(s)) {
        printf("Stack Underflow\n");
        exit(1);
    }
    return s->data[s->top--];
}

// Peek at top element
int peek(Stack *s) {
    if (isEmpty(s)) {
        printf("Stack is empty\n");
        exit(1);
    }
    return s->data[s->top];
}

int main() {
    Stack s;
    initStack(&s);
    
    push(&s, 10);
    push(&s, 20);
    push(&s, 30);
    
    printf("Top element: %d\n", peek(&s));
    printf("Popped element: %d\n", pop(&s));
    printf("Popped element: %d\n", pop(&s));
    
    return 0;
}
```

### Linked List Implementation in C

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

typedef struct Node {
    int data;
    struct Node *next;
} Node;

typedef struct {
    Node *top;
} Stack;

// Initialize stack
void initStack(Stack *s) {
    s->top = NULL;
}

// Check if stack is empty
bool isEmpty(Stack *s) {
    return s->top == NULL;
}

// Push element onto stack
void push(Stack *s, int value) {
    Node *newNode = (Node*)malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Memory allocation failed\n");
        return;
    }
    newNode->data = value;
    newNode->next = s->top;
    s->top = newNode;
}

// Pop element from stack
int pop(Stack *s) {
    if (isEmpty(s)) {
        printf("Stack Underflow\n");
        exit(1);
    }
    Node *temp = s->top;
    int value = temp->data;
    s->top = s->top->next;
    free(temp);
    return value;
}

// Peek at top element
int peek(Stack *s) {
    if (isEmpty(s)) {
        printf("Stack is empty\n");
        exit(1);
    }
    return s->top->data;
}

int main() {
    Stack s;
    initStack(&s);
    
    push(&s, 10);
    push(&s, 20);
    push(&s, 30);
    
    printf("Top element: %d\n", peek(&s));
    printf("Popped element: %d\n", pop(&s));
    printf("Popped element: %d\n", pop(&s));
    
    return 0;
}
```

## Queues

### Definition
A queue is a First-In-First-Out (FIFO) data structure where the first element added is the first one to be removed. Think of it like a line of people waiting - the first person in line is the first to be served.

### Operations
1. **Enqueue**: Add an element to the rear of the queue
2. **Dequeue**: Remove and return the front element from the queue
3. **Front**: View the front element without removing it
4. **Rear**: View the rear element without removing it
5. **isEmpty**: Check if the queue is empty
6. **isFull**: Check if the queue is full (for array implementation)

### Implementation Approaches
1. **Array Implementation**: Fixed size, can have wasted space or need circular approach
2. **Linked List Implementation**: Dynamic size, more flexible but uses more memory

### Variations
1. **Circular Queue**: Efficient array implementation that reuses space
2. **Priority Queue**: Elements have priority and are served accordingly
3. **Double-Ended Queue (Deque)**: Allows insertion and removal at both ends

### Applications
- CPU scheduling
- Disk scheduling
- Breadth-First Search (BFS) algorithm
- Print job management
- Call center phone systems

### Array Implementation in C

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

#define MAX_SIZE 100

typedef struct {
    int data[MAX_SIZE];
    int front;
    int rear;
} Queue;

// Initialize queue
void initQueue(Queue *q) {
    q->front = -1;
    q->rear = -1;
}

// Check if queue is empty
bool isEmpty(Queue *q) {
    return q->front == -1;
}

// Check if queue is full
bool isFull(Queue *q) {
    return (q->rear + 1) % MAX_SIZE == q->front;
}

// Add element to queue
void enqueue(Queue *q, int value) {
    if (isFull(q)) {
        printf("Queue Overflow\n");
        return;
    }
    if (isEmpty(q)) {
        q->front = q->rear = 0;
    } else {
        q->rear = (q->rear + 1) % MAX_SIZE;
    }
    q->data[q->rear] = value;
}

// Remove element from queue
int dequeue(Queue *q) {
    if (isEmpty(q)) {
        printf("Queue Underflow\n");
        exit(1);
    }
    int value = q->data[q->front];
    if (q->front == q->rear) {
        q->front = q->rear = -1;
    } else {
        q->front = (q->front + 1) % MAX_SIZE;
    }
    return value;
}

// Get front element
int front(Queue *q) {
    if (isEmpty(q)) {
        printf("Queue is empty\n");
        exit(1);
    }
    return q->data[q->front];
}

// Get rear element
int rear(Queue *q) {
    if (isEmpty(q)) {
        printf("Queue is empty\n");
        exit(1);
    }
    return q->data[q->rear];
}

int main() {
    Queue q;
    initQueue(&q);
    
    enqueue(&q, 10);
    enqueue(&q, 20);
    enqueue(&q, 30);
    
    printf("Front element: %d\n", front(&q));
    printf("Dequeued element: %d\n", dequeue(&q));
    printf("Dequeued element: %d\n", dequeue(&q));
    
    return 0;
}
```

### Linked List Implementation in C

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

typedef struct Node {
    int data;
    struct Node *next;
} Node;

typedef struct {
    Node *front;
    Node *rear;
} Queue;

// Initialize queue
void initQueue(Queue *q) {
    q->front = q->rear = NULL;
}

// Check if queue is empty
bool isEmpty(Queue *q) {
    return q->front == NULL;
}

// Add element to queue
void enqueue(Queue *q, int value) {
    Node *newNode = (Node*)malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Memory allocation failed\n");
        return;
    }
    newNode->data = value;
    newNode->next = NULL;
    
    if (isEmpty(q)) {
        q->front = q->rear = newNode;
    } else {
        q->rear->next = newNode;
        q->rear = newNode;
    }
}

// Remove element from queue
int dequeue(Queue *q) {
    if (isEmpty(q)) {
        printf("Queue Underflow\n");
        exit(1);
    }
    Node *temp = q->front;
    int value = temp->data;
    q->front = q->front->next;
    
    if (q->front == NULL) {
        q->rear = NULL;
    }
    
    free(temp);
    return value;
}

// Get front element
int front(Queue *q) {
    if (isEmpty(q)) {
        printf("Queue is empty\n");
        exit(1);
    }
    return q->front->data;
}

// Get rear element
int rear(Queue *q) {
    if (isEmpty(q)) {
        printf("Queue is empty\n");
        exit(1);
    }
    return q->rear->data;
}

int main() {
    Queue q;
    initQueue(&q);
    
    enqueue(&q, 10);
    enqueue(&q, 20);
    enqueue(&q, 30);
    
    printf("Front element: %d\n", front(&q));
    printf("Dequeued element: %d\n", dequeue(&q));
    printf("Dequeued element: %d\n", dequeue(&q));
    
    return 0;
}
```

## Comparison of Stacks and Queues

| Feature          | Stack                          | Queue                          |
|------------------|--------------------------------|--------------------------------|
| Order principle  | LIFO (Last In First Out)       | FIFO (First In First Out)      |
| Operations       | push, pop, peek                | enqueue, dequeue, front, rear  |
| Insertion point  | Only at top (for simple stack) | At rear                        |
| Removal point    | Only from top                  | Only from front                |
| Typical uses     | Function calls, undo/redo      | Task scheduling, BFS           |

## Time Complexity Analysis

### Stack Operations
- **Array Implementation**:
  - Push: O(1)
  - Pop: O(1)
  - Peek: O(1)
  - Search: O(n)
  
- **Linked List Implementation**:
  - Push: O(1)
  - Pop: O(1)
  - Peek: O(1)
  - Search: O(n)

### Queue Operations
- **Array Implementation**:
  - Enqueue: O(1)
  - Dequeue: O(1)
  - Front/Rear: O(1)
  - Search: O(n)
  
- **Linked List Implementation**:
  - Enqueue: O(1)
  - Dequeue: O(1)
  - Front/Rear: O(1)
  - Search: O(n)

## Practical Considerations

1. **Choice of Implementation**:
   - Arrays are better when the maximum size is known and memory is a concern
   - Linked lists are better when the size is unpredictable or frequently changes

2. **Memory Usage**:
   - Arrays have fixed size but less overhead per element
   - Linked lists use more memory per element but can grow dynamically

3. **Performance**:
   - Both implementations offer O(1) for core operations
   - Array implementations may have better cache performance

4. **Error Handling**:
   - Always check for underflow (empty stack/queue) and overflow (full stack/queue)
   - Consider returning error codes instead of exiting the program in production code

## Advanced Topics

1. **Multiple Stacks/Queues in a Single Array**:
   - Can be implemented by dividing the array into sections
   - Useful when memory is constrained

2. **Stack/Queue Hybrids**:
   - Deque (Double-ended queue) allows insertion/removal at both ends
   - Can function as both stack and queue

3. **Thread-Safe Implementations**:
   - Important for multi-threaded applications
   - Requires synchronization mechanisms like mutexes

4. **Persistent Stacks/Queues**:
   - Maintain multiple versions of the data structure
   - Useful in functional programming and undo/redo systems

## Conclusion

Stacks and queues are fundamental data structures that every programmer should understand thoroughly. Their simplicity belies their importance - they form the backbone of many algorithms and system operations. The choice between array and linked list implementations depends on your specific requirements regarding memory usage, flexibility, and performance.
