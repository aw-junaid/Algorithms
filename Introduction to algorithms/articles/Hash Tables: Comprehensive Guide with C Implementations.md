# Hash Tables: Comprehensive Guide with C Implementations

## 1. Direct-Address Tables

### Concept
Direct-address tables are the simplest form of hash tables where keys directly correspond to indices in an array. This approach works well when the universe of possible keys is small.

### Characteristics
- Each slot in the array corresponds to a unique key
- No collisions occur since each key has its own slot
- Space inefficient for large key universes
- Time complexity: O(1) for all operations

### C Implementation

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>

#define SIZE 100

typedef struct {
    int key;
    char* value;
} DirectAddressItem;

typedef struct {
    DirectAddressItem* table[SIZE];
} DirectAddressTable;

DirectAddressTable* createDirectAddressTable() {
    DirectAddressTable* dat = (DirectAddressTable*)malloc(sizeof(DirectAddressTable));
    for (int i = 0; i < SIZE; i++) {
        dat->table[i] = NULL;
    }
    return dat;
}

void directAddressInsert(DirectAddressTable* dat, int key, char* value) {
    if (key < 0 || key >= SIZE) {
        printf("Key out of bounds\n");
        return;
    }
    DirectAddressItem* newItem = (DirectAddressItem*)malloc(sizeof(DirectAddressItem));
    newItem->key = key;
    newItem->value = value;
    dat->table[key] = newItem;
}

char* directAddressSearch(DirectAddressTable* dat, int key) {
    if (key < 0 || key >= SIZE) {
        printf("Key out of bounds\n");
        return NULL;
    }
    if (dat->table[key] != NULL) {
        return dat->table[key]->value;
    }
    return NULL;
}

void directAddressDelete(DirectAddressTable* dat, int key) {
    if (key < 0 || key >= SIZE) {
        printf("Key out of bounds\n");
        return;
    }
    if (dat->table[key] != NULL) {
        free(dat->table[key]);
        dat->table[key] = NULL;
    }
}

void destroyDirectAddressTable(DirectAddressTable* dat) {
    for (int i = 0; i < SIZE; i++) {
        if (dat->table[i] != NULL) {
            free(dat->table[i]);
        }
    }
    free(dat);
}

int main() {
    DirectAddressTable* dat = createDirectAddressTable();
    
    directAddressInsert(dat, 5, "Apple");
    directAddressInsert(dat, 20, "Banana");
    directAddressInsert(dat, 30, "Cherry");
    
    printf("Key 5: %s\n", directAddressSearch(dat, 5));
    printf("Key 20: %s\n", directAddressSearch(dat, 20));
    printf("Key 30: %s\n", directAddressSearch(dat, 30));
    
    directAddressDelete(dat, 20);
    printf("After deletion, Key 20: %s\n", directAddressSearch(dat, 20));
    
    destroyDirectAddressTable(dat);
    return 0;
}
```

## 2. Hash Tables

### Concept
Hash tables are data structures that map keys to values using a hash function to compute an index into an array of buckets or slots. They provide efficient O(1) average-case time complexity for insert, delete, and search operations.

### Key Components
1. **Hash function**: Maps keys to indices
2. **Collision resolution**: Handles cases when multiple keys map to the same index
3. **Load factor**: Ratio of number of elements to table size (important for performance)

### C Implementation (Chaining Method)

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define TABLE_SIZE 10

typedef struct Node {
    char* key;
    char* value;
    struct Node* next;
} Node;

typedef struct {
    Node* buckets[TABLE_SIZE];
} HashTable;

unsigned int hashFunction(const char* key) {
    unsigned int hash = 0;
    for (int i = 0; key[i] != '\0'; i++) {
        hash = 31 * hash + key[i];
    }
    return hash % TABLE_SIZE;
}

HashTable* createHashTable() {
    HashTable* ht = (HashTable*)malloc(sizeof(HashTable));
    for (int i = 0; i < TABLE_SIZE; i++) {
        ht->buckets[i] = NULL;
    }
    return ht;
}

void insert(HashTable* ht, const char* key, const char* value) {
    unsigned int index = hashFunction(key);
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->key = strdup(key);
    newNode->value = strdup(value);
    newNode->next = NULL;
    
    if (ht->buckets[index] == NULL) {
        ht->buckets[index] = newNode;
    } else {
        // Collision occurred, add to the front of the list
        newNode->next = ht->buckets[index];
        ht->buckets[index] = newNode;
    }
}

char* search(HashTable* ht, const char* key) {
    unsigned int index = hashFunction(key);
    Node* current = ht->buckets[index];
    
    while (current != NULL) {
        if (strcmp(current->key, key) == 0) {
            return current->value;
        }
        current = current->next;
    }
    return NULL;
}

void delete(HashTable* ht, const char* key) {
    unsigned int index = hashFunction(key);
    Node* current = ht->buckets[index];
    Node* prev = NULL;
    
    while (current != NULL) {
        if (strcmp(current->key, key) == 0) {
            if (prev == NULL) {
                // First node in the bucket
                ht->buckets[index] = current->next;
            } else {
                prev->next = current->next;
            }
            free(current->key);
            free(current->value);
            free(current);
            return;
        }
        prev = current;
        current = current->next;
    }
}

void destroyHashTable(HashTable* ht) {
    for (int i = 0; i < TABLE_SIZE; i++) {
        Node* current = ht->buckets[i];
        while (current != NULL) {
            Node* temp = current;
            current = current->next;
            free(temp->key);
            free(temp->value);
            free(temp);
        }
    }
    free(ht);
}

int main() {
    HashTable* ht = createHashTable();
    
    insert(ht, "apple", "A fruit");
    insert(ht, "banana", "Another fruit");
    insert(ht, "carrot", "A vegetable");
    
    printf("apple: %s\n", search(ht, "apple"));
    printf("banana: %s\n", search(ht, "banana"));
    printf("carrot: %s\n", search(ht, "carrot"));
    
    delete(ht, "banana");
    printf("After deletion, banana: %s\n", search(ht, "banana"));
    
    destroyHashTable(ht);
    return 0;
}
```

## 3. Hash Functions

### Concept
A hash function maps data of arbitrary size to fixed-size values (hash codes). Good hash functions should:
- Distribute keys uniformly across the hash table
- Minimize collisions
- Be fast to compute

### Common Hash Functions

1. **Division Method**: `h(k) = k mod m`
   - Simple but sensitive to choice of m (should be prime)
   
2. **Multiplication Method**: `h(k) = floor(m * (k * A mod 1))`
   - A is a constant (0 < A < 1)
   - Knuth suggests A ≈ (√5 - 1)/2 ≈ 0.6180339887

3. **Universal Hashing**: Randomly selects a hash function from a family of functions

4. **For Strings**:
   - Simple: Sum of ASCII values
   - Better: Polynomial rolling hash (as in our implementation)

### C Implementation of Various Hash Functions

```c
#include <stdio.h>
#include <string.h>

// Division method for integers
unsigned int divisionHash(int key, unsigned int tableSize) {
    return key % tableSize;
}

// Multiplication method for integers
unsigned int multiplicationHash(int key, unsigned int tableSize) {
    const double A = 0.6180339887; // (sqrt(5) - 1) / 2
    double val = key * A;
    val -= (unsigned int)val; // Get fractional part
    return (unsigned int)(tableSize * val);
}

// Basic string hash (sum of characters)
unsigned int basicStringHash(const char* key, unsigned int tableSize) {
    unsigned int hash = 0;
    for (int i = 0; key[i] != '\0'; i++) {
        hash += key[i];
    }
    return hash % tableSize;
}

// DJB2 string hash (popular and effective)
unsigned int djb2Hash(const char* key, unsigned int tableSize) {
    unsigned int hash = 5381;
    int c;
    
    while ((c = *key++)) {
        hash = ((hash << 5) + hash) + c; // hash * 33 + c
    }
    
    return hash % tableSize;
}

// Polynomial rolling hash for strings
unsigned int polynomialRollingHash(const char* key, unsigned int tableSize) {
    const int p = 31; // Prime number
    const int m = 1e9 + 9; // Large prime number
    unsigned int hash = 0;
    unsigned int p_pow = 1;
    
    for (int i = 0; key[i] != '\0'; i++) {
        hash = (hash + (key[i] - 'a' + 1) * p_pow) % m;
        p_pow = (p_pow * p) % m;
    }
    
    return hash % tableSize;
}

int main() {
    const char* str = "hello";
    unsigned int tableSize = 100;
    
    printf("Division hash for 42: %u\n", divisionHash(42, tableSize));
    printf("Multiplication hash for 42: %u\n", multiplicationHash(42, tableSize));
    printf("Basic string hash for '%s': %u\n", str, basicStringHash(str, tableSize));
    printf("DJB2 hash for '%s': %u\n", str, djb2Hash(str, tableSize));
    printf("Polynomial rolling hash for '%s': %u\n", str, polynomialRollingHash(str, tableSize));
    
    return 0;
}
```

## 4. Open Addressing

### Concept
Open addressing is a collision resolution method where all elements are stored in the hash table itself. When a collision occurs, the algorithm probes through the table to find the next empty slot.

### Probing Methods
1. **Linear Probing**: `h(k, i) = (h'(k) + i) mod m`
   - Simple but suffers from clustering
   
2. **Quadratic Probing**: `h(k, i) = (h'(k) + c1*i + c2*i²) mod m`
   - Reduces clustering but may not find empty slot even if one exists
   
3. **Double Hashing**: `h(k, i) = (h1(k) + i*h2(k)) mod m`
   - Uses two hash functions to compute probe sequence
   - Generally the best open addressing method

### C Implementation (Linear Probing)

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define TABLE_SIZE 10
#define DELETED_NODE (Node*)(0xFFFFFFFFFFFFFFFFUL)

typedef struct {
    char* key;
    char* value;
} Node;

typedef struct {
    Node* nodes[TABLE_SIZE];
    int size;
} HashTable;

unsigned int hashFunction(const char* key) {
    unsigned int hash = 0;
    for (int i = 0; key[i] != '\0'; i++) {
        hash = 31 * hash + key[i];
    }
    return hash % TABLE_SIZE;
}

HashTable* createHashTable() {
    HashTable* ht = (HashTable*)malloc(sizeof(HashTable));
    for (int i = 0; i < TABLE_SIZE; i++) {
        ht->nodes[i] = NULL;
    }
    ht->size = 0;
    return ht;
}

void insert(HashTable* ht, const char* key, const char* value) {
    if (ht->size >= TABLE_SIZE) {
        printf("Table is full\n");
        return;
    }
    
    unsigned int index = hashFunction(key);
    int i = 0;
    
    while (i < TABLE_SIZE) {
        int currentIndex = (index + i) % TABLE_SIZE;
        
        if (ht->nodes[currentIndex] == NULL || ht->nodes[currentIndex] == DELETED_NODE) {
            Node* newNode = (Node*)malloc(sizeof(Node));
            newNode->key = strdup(key);
            newNode->value = strdup(value);
            ht->nodes[currentIndex] = newNode;
            ht->size++;
            return;
        } else if (strcmp(ht->nodes[currentIndex]->key, key) == 0) {
            // Key already exists, update value
            free(ht->nodes[currentIndex]->value);
            ht->nodes[currentIndex]->value = strdup(value);
            return;
        }
        i++;
    }
    
    printf("Could not insert key %s\n", key);
}

char* search(HashTable* ht, const char* key) {
    unsigned int index = hashFunction(key);
    int i = 0;
    
    while (i < TABLE_SIZE) {
        int currentIndex = (index + i) % TABLE_SIZE;
        
        if (ht->nodes[currentIndex] == NULL) {
            return NULL;
        } else if (ht->nodes[currentIndex] != DELETED_NODE && 
                   strcmp(ht->nodes[currentIndex]->key, key) == 0) {
            return ht->nodes[currentIndex]->value;
        }
        i++;
    }
    
    return NULL;
}

void delete(HashTable* ht, const char* key) {
    unsigned int index = hashFunction(key);
    int i = 0;
    
    while (i < TABLE_SIZE) {
        int currentIndex = (index + i) % TABLE_SIZE;
        
        if (ht->nodes[currentIndex] == NULL) {
            return;
        } else if (ht->nodes[currentIndex] != DELETED_NODE && 
                   strcmp(ht->nodes[currentIndex]->key, key) == 0) {
            free(ht->nodes[currentIndex]->key);
            free(ht->nodes[currentIndex]->value);
            free(ht->nodes[currentIndex]);
            ht->nodes[currentIndex] = DELETED_NODE;
            ht->size--;
            return;
        }
        i++;
    }
}

void destroyHashTable(HashTable* ht) {
    for (int i = 0; i < TABLE_SIZE; i++) {
        if (ht->nodes[i] != NULL && ht->nodes[i] != DELETED_NODE) {
            free(ht->nodes[i]->key);
            free(ht->nodes[i]->value);
            free(ht->nodes[i]);
        }
    }
    free(ht);
}

int main() {
    HashTable* ht = createHashTable();
    
    insert(ht, "apple", "A fruit");
    insert(ht, "banana", "Another fruit");
    insert(ht, "carrot", "A vegetable");
    insert(ht, "apple", "Red fruit"); // Update
    
    printf("apple: %s\n", search(ht, "apple"));
    printf("banana: %s\n", search(ht, "banana"));
    printf("carrot: %s\n", search(ht, "carrot"));
    printf("grape: %s\n", search(ht, "grape"));
    
    delete(ht, "banana");
    printf("After deletion, banana: %s\n", search(ht, "banana"));
    
    destroyHashTable(ht);
    return 0;
}
```

## 5. Perfect Hashing

### Concept
Perfect hashing refers to a hash function that maps distinct elements to a set of integers with no collisions. There are two levels:
1. First level: Similar to standard hashing with chaining
2. Second level: Each first-level bucket has its own collision-free hash function

### Characteristics
- O(1) worst-case time for lookups
- Requires O(n) construction time for static sets
- Space can be O(n) with careful implementation
- Mainly used when the set of keys is static and known in advance

### C Implementation (Two-Level Perfect Hashing)

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#define MAX_FIRST_LEVEL 10
#define MAX_SECOND_LEVEL 20

typedef struct {
    char* key;
    char* value;
} PerfectHashItem;

typedef struct {
    PerfectHashItem* items;
    int size;
    int a, b; // Parameters for the second hash function
    int prime; // Prime number for second hash
} SecondLevelTable;

typedef struct {
    SecondLevelTable* tables[MAX_FIRST_LEVEL];
    int a, b; // Parameters for the first hash function
    int prime; // Prime number for first hash
    int size;
} PerfectHashTable;

// Universal hash function: h(k) = ((a*k + b) mod p) mod m
unsigned int universalHash(int a, int b, int prime, const char* key, int m) {
    unsigned int hash = 0;
    for (int i = 0; key[i] != '\0'; i++) {
        hash = (hash * 31 + key[i]) % prime;
    }
    return ((a * hash + b) % prime) % m;
}

// Check if a number is prime
int isPrime(int n) {
    if (n <= 1) return 0;
    if (n <= 3) return 1;
    if (n % 2 == 0 || n % 3 == 0) return 0;
    for (int i = 5; i * i <= n; i += 6) {
        if (n % i == 0 || n % (i + 2) == 0) return 0;
    }
    return 1;
}

// Find a prime number greater than n
int findPrime(int n) {
    while (!isPrime(n)) {
        n++;
    }
    return n;
}

// Create a second level table
SecondLevelTable* createSecondLevelTable(char** keys, char** values, int count, int prime) {
    if (count == 0) return NULL;
    
    SecondLevelTable* slt = (SecondLevelTable*)malloc(sizeof(SecondLevelTable));
    slt->size = count * count; // Square the count for perfect hashing
    slt->items = (PerfectHashItem*)calloc(slt->size, sizeof(PerfectHashItem));
    slt->prime = prime;
    
    // Try random a and b until we find a perfect hash
    int attempts = 0;
    int maxAttempts = 100;
    int collision = 1;
    
    srand(time(NULL));
    
    while (collision && attempts < maxAttempts) {
        collision = 0;
        slt->a = rand() % (prime - 1) + 1;
        slt->b = rand() % prime;
        
        // Clear the items array
        memset(slt->items, 0, slt->size * sizeof(PerfectHashItem));
        
        for (int i = 0; i < count; i++) {
            unsigned int hash = universalHash(slt->a, slt->b, slt->prime, keys[i], slt->size);
            
            if (slt->items[hash].key != NULL) {
                collision = 1;
                break;
            }
            
            slt->items[hash].key = strdup(keys[i]);
            slt->items[hash].value = strdup(values[i]);
        }
        
        attempts++;
    }
    
    if (collision) {
        // Fallback: use a larger table
        free(slt->items);
        slt->size = count * count * 2;
        slt->items = (PerfectHashItem*)calloc(slt->size, sizeof(PerfectHashItem));
        
        do {
            collision = 0;
            slt->a = rand() % (prime - 1) + 1;
            slt->b = rand() % prime;
            
            memset(slt->items, 0, slt->size * sizeof(PerfectHashItem));
            
            for (int i = 0; i < count; i++) {
                unsigned int hash = universalHash(slt->a, slt->b, slt->prime, keys[i], slt->size);
                
                if (slt->items[hash].key != NULL) {
                    collision = 1;
                    break;
                }
                
                slt->items[hash].key = strdup(keys[i]);
                slt->items[hash].value = strdup(values[i]);
            }
        } while (collision);
    }
    
    return slt;
}

PerfectHashTable* createPerfectHashTable(char** keys, char** values, int count) {
    if (count == 0) return NULL;
    
    PerfectHashTable* pht = (PerfectHashTable*)malloc(sizeof(PerfectHashTable));
    pht->prime = findPrime(count * 2);
    pht->size = count;
    
    // Temporary storage for keys in each first-level bucket
    char** bucketKeys[MAX_FIRST_LEVEL] = {0};
    char** bucketValues[MAX_FIRST_LEVEL] = {0};
    int bucketSizes[MAX_FIRST_LEVEL] = {0};
    
    // Allocate memory for bucket arrays
    for (int i = 0; i < MAX_FIRST_LEVEL; i++) {
        bucketKeys[i] = (char**)malloc(count * sizeof(char*));
        bucketValues[i] = (char**)malloc(count * sizeof(char*));
    }
    
    // Try random a and b until the sum of squares of bucket sizes is O(n)
    int sumOfSquares;
    int attempts = 0;
    int maxAttempts = 100;
    
    srand(time(NULL));
    
    do {
        sumOfSquares = 0;
        pht->a = rand() % (pht->prime - 1) + 1;
        pht->b = rand() % pht->prime;
        
        // Reset bucket sizes
        memset(bucketSizes, 0, MAX_FIRST_LEVEL * sizeof(int));
        
        // Distribute keys to first-level buckets
        for (int i = 0; i < count; i++) {
            unsigned int hash = universalHash(pht->a, pht->b, pht->prime, keys[i], MAX_FIRST_LEVEL);
            bucketKeys[hash][bucketSizes[hash]] = keys[i];
            bucketValues[hash][bucketSizes[hash]] = values[i];
            bucketSizes[hash]++;
        }
        
        // Calculate sum of squares
        sumOfSquares = 0;
        for (int i = 0; i < MAX_FIRST_LEVEL; i++) {
            sumOfSquares += bucketSizes[i] * bucketSizes[i];
        }
        
        attempts++;
    } while (sumOfSquares > 4 * count && attempts < maxAttempts);
    
    // Create second-level tables
    for (int i = 0; i < MAX_FIRST_LEVEL; i++) {
        if (bucketSizes[i] > 0) {
            pht->tables[i] = createSecondLevelTable(bucketKeys[i], bucketValues[i], bucketSizes[i], pht->prime);
        } else {
            pht->tables[i] = NULL;
        }
        free(bucketKeys[i]);
        free(bucketValues[i]);
    }
    
    return pht;
}

char* perfectHashSearch(PerfectHashTable* pht, const char* key) {
    unsigned int firstHash = universalHash(pht->a, pht->b, pht->prime, key, MAX_FIRST_LEVEL);
    
    if (pht->tables[firstHash] == NULL) {
        return NULL;
    }
    
    SecondLevelTable* slt = pht->tables[firstHash];
    unsigned int secondHash = universalHash(slt->a, slt->b, slt->prime, key, slt->size);
    
    if (slt->items[secondHash].key != NULL && 
        strcmp(slt->items[secondHash].key, key) == 0) {
        return slt->items[secondHash].value;
    }
    
    return NULL;
}

void destroyPerfectHashTable(PerfectHashTable* pht) {
    for (int i = 0; i < MAX_FIRST_LEVEL; i++) {
        if (pht->tables[i] != NULL) {
            for (int j = 0; j < pht->tables[i]->size; j++) {
                if (pht->tables[i]->items[j].key != NULL) {
                    free(pht->tables[i]->items[j].key);
                    free(pht->tables[i]->items[j].value);
                }
            }
            free(pht->tables[i]->items);
            free(pht->tables[i]);
        }
    }
    free(pht);
}

int main() {
    char* keys[] = {"apple", "banana", "cherry", "date", "elderberry", "fig", "grape"};
    char* values[] = {"red fruit", "yellow fruit", "red berry", "sweet fruit", 
                     "dark berry", "purple fruit", "green fruit"};
    int count = sizeof(keys) / sizeof(keys[0]);
    
    PerfectHashTable* pht = createPerfectHashTable(keys, values, count);
    
    printf("apple: %s\n", perfectHashSearch(pht, "apple"));
    printf("banana: %s\n", perfectHashSearch(pht, "banana"));
    printf("cherry: %s\n", perfectHashSearch(pht, "cherry"));
    printf("date: %s\n", perfectHashSearch(pht, "date"));
    printf("elderberry: %s\n", perfectHashSearch(pht, "elderberry"));
    printf("fig: %s\n", perfectHashSearch(pht, "fig"));
    printf("grape: %s\n", perfectHashSearch(pht, "grape"));
    printf("kiwi: %s\n", perfectHashSearch(pht, "kiwi"));
    
    destroyPerfectHashTable(pht);
    return 0;
}
```

## Summary of Hash Table Techniques

1. **Direct-Address Tables**: Simple but inefficient for large key spaces
2. **Hash Tables with Chaining**: Uses linked lists to handle collisions
3. **Hash Functions**: Critical for performance, should distribute keys uniformly
4. **Open Addressing**: Stores all elements in the table itself with probing
5. **Perfect Hashing**: Guarantees O(1) lookups for static key sets

Each approach has its trade-offs between space complexity, time complexity, and implementation complexity. The choice depends on the specific requirements of your application, such as whether the key set is static or dynamic, the expected load factor, and the importance of worst-case versus average-case performance.
