# The Rabin-Karp Algorithm: A Detailed Explanation

The Rabin-Karp algorithm is a string searching algorithm that uses hashing to find patterns in text. It was developed by Michael O. Rabin and Richard M. Karp in 1987.

## How the Rabin-Karp Algorithm Works

The algorithm works by comparing hash values of the pattern with hash values of substrings in the text. Here's a step-by-step breakdown:

### 1. Basic Concept

Instead of comparing the pattern directly with every possible substring in the text (which would be O(nm) time complexity for text length n and pattern length m), Rabin-Karp uses:

1. A hash function to compute a hash value for the pattern
2. The same hash function to compute hash values for all possible m-length substrings in the text
3. Comparison of pattern hash with substring hashes
4. Only when hashes match, a full string comparison is performed (to handle hash collisions)

### 2. Hash Function

The key innovation is using a rolling hash function that allows computing the hash of the next substring in constant time based on the previous substring's hash.

The most common hash function used is:
```
hash(S) = (S[0]*d^(m-1) + S[1]*d^(m-2) + ... + S[m-1]) mod q
```
Where:
- `d` is the number of characters in the alphabet (typically 256 for ASCII)
- `q` is a prime number to reduce hash collisions
- `m` is the pattern length

### 3. Rolling Hash Calculation

For a text "T" and pattern length "m", the hash of the first window is:
```
h0 = (T[0]*d^(m-1) + T[1]*d^(m-2) + ... + T[m-1]) mod q
```

The hash of the next window (shifted by 1) can be computed as:
```
h1 = (d*(h0 - T[0]*d^(m-1)) + T[m]) mod q
```
This allows constant-time hash computation for each new window.

### 4. Algorithm Steps

1. Compute the hash value of the pattern
2. Compute the hash value of the first window in the text
3. Compare the two hash values:
   - If they match, verify by comparing characters
   - If they don't match, compute hash for next window
4. Repeat until all possible windows are checked

### 5. Time Complexity

- Average case: O(n + m)
- Worst case: O(nm) (when all substrings have the same hash as the pattern)
- Best case: O(n + m)

## Advantages

1. Efficient for multiple pattern searches (can compute all pattern hashes once)
2. Works well for plagiarism detection and finding similarities in documents
3. Can be extended to 2D pattern matching

## Disadvantages

1. Worst-case performance is poor (though unlikely with good hash function)
2. Requires careful selection of hash parameters to minimize collisions

## C Implementation

Here's a complete implementation in C:

```c
#include <stdio.h>
#include <string.h>
#include <math.h>

#define d 256 // Number of characters in the input alphabet

void search(char pattern[], char text[], int q) {
    int M = strlen(pattern);
    int N = strlen(text);
    int i, j;
    int p = 0; // hash value for pattern
    int t = 0; // hash value for text
    int h = 1;

    // The value of h would be "pow(d, M-1)%q"
    for (i = 0; i < M-1; i++)
        h = (h * d) % q;

    // Calculate the hash value of pattern and first window of text
    for (i = 0; i < M; i++) {
        p = (d * p + pattern[i]) % q;
        t = (d * t + text[i]) % q;
    }

    // Slide the pattern over text one by one
    for (i = 0; i <= N - M; i++) {
        // Check the hash values of current window of text and pattern
        // If the hash values match then only check for characters one by one
        if (p == t) {
            // Check for characters one by one
            for (j = 0; j < M; j++) {
                if (text[i+j] != pattern[j])
                    break;
            }

            if (j == M) // if p == t and pattern[0...M-1] = text[i, i+1, ...i+M-1]
                printf("Pattern found at index %d \n", i);
        }

        // Calculate hash value for next window of text: Remove leading digit,
        // add trailing digit
        if (i < N - M) {
            t = (d * (t - text[i] * h) + text[i+M]) % q;

            // We might get negative value of t, converting it to positive
            if (t < 0)
                t = (t + q);
        }
    }
}

int main() {
    char text[] = "GEEKS FOR GEEKS";
    char pattern[] = "GEEK";
    int q = 101; // A prime number
    search(pattern, text, q);
    return 0;
}
```

---
