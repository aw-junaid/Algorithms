# The Knuth-Morris-Pratt (KMP) Algorithm: Detailed Explanation

The Knuth-Morris-Pratt (KMP) algorithm is a linear-time string matching algorithm that improves upon the naive pattern matching approach by using preprocessing to avoid unnecessary character comparisons.

## Key Concepts

### 1. Problem Statement
Given a text string `T` of length `n` and a pattern string `P` of length `m`, find all occurrences of `P` in `T`.

### 2. Naive Approach Limitations
The naive approach checks for pattern matches at every possible starting position in the text, resulting in O(n*m) time complexity in the worst case.

### 3. KMP Optimization
KMP achieves O(n+m) time complexity by:
- Preprocessing the pattern to create a "partial match" table (failure function)
- Using this table to skip unnecessary comparisons when a mismatch occurs

## The Partial Match Table (Failure Function)

The key insight is that when a mismatch occurs, the pattern may have a prefix that matches a suffix of the already matched portion.

For a pattern `P = "ababaca"`, the partial match table `π` is:

| Index | Substring | Longest Proper Prefix/Suffix | π Value |
|-------|-----------|------------------------------|---------|
| 0     | a         | ""                           | 0       |
| 1     | ab        | ""                           | 0       |
| 2     | aba       | "a"                          | 1       |
| 3     | abab      | "ab"                         | 2       |
| 4     | ababa     | "aba"                        | 3       |
| 5     | ababac    | ""                           | 0       |
| 6     | ababaca   | "a"                          | 1       |

## Algorithm Steps

1. **Preprocessing (Build Partial Match Table)**
   - Compute the longest proper prefix which is also suffix for each pattern prefix
   - Store lengths in table `π`

2. **Searching**
   - Compare text and pattern characters
   - On mismatch, use `π` table to determine next comparison position
   - Avoid re-checking matched characters

## Mathematical Representation

The failure function π for a pattern P is defined as:

π[q] = max{k | k < q and Pₖ ⊐ P_q}

Where:
- Pₖ is the prefix of P of length k
- P_q is the prefix of P of length q
- ⊐ denotes "is a proper suffix of"

## C Implementation

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

// Compute the partial match table (failure function)
void computeLPSArray(char* pattern, int m, int* lps) {
    int len = 0; // length of the previous longest prefix suffix
    lps[0] = 0;  // lps[0] is always 0
    
    int i = 1;
    while (i < m) {
        if (pattern[i] == pattern[len]) {
            len++;
            lps[i] = len;
            i++;
        } else {
            if (len != 0) {
                len = lps[len - 1];
            } else {
                lps[i] = 0;
                i++;
            }
        }
    }
}

// KMP search algorithm
void KMPSearch(char* pattern, char* text) {
    int m = strlen(pattern);
    int n = strlen(text);
    
    // Create lps array and compute it
    int* lps = (int*)malloc(m * sizeof(int));
    computeLPSArray(pattern, m, lps);
    
    int i = 0; // index for text[]
    int j = 0; // index for pattern[]
    
    while (i < n) {
        if (pattern[j] == text[i]) {
            j++;
            i++;
        }
        
        if (j == m) {
            printf("Pattern found at index %d\n", i - j);
            j = lps[j - 1];
        } else if (i < n && pattern[j] != text[i]) {
            if (j != 0) {
                j = lps[j - 1];
            } else {
                i++;
            }
        }
    }
    
    free(lps);
}

int main() {
    char text[] = "ABABDABACDABABCABAB";
    char pattern[] = "ABABCABAB";
    
    printf("Text: %s\n", text);
    printf("Pattern: %s\n\n", pattern);
    printf("Pattern found at positions:\n");
    
    KMPSearch(pattern, text);
    
    return 0;
}
```

## WordPress-Compatible Mathematical Notation

For WordPress sites, you can represent the mathematical equations using LaTeX notation with plugins like MathJax:

The failure function can be represented as:

`\pi[q] = \max\{k | k < q \text{ and } P_k \sqsupset P_q\}`

Where:
- `\pi[q]` is the failure function value at position q
- `P_k` is the prefix of length k
- `\sqsupset` denotes "is a proper suffix of"

## Time Complexity Analysis

- **Preprocessing**: O(m) where m is pattern length
- **Searching**: O(n) where n is text length
- **Total**: O(n + m)

This is a significant improvement over the naive O(n*m) approach, especially for long texts with repetitive patterns.

## Applications

1. Text editors (search functionality)
2. DNA sequence matching
3. Plagiarism detection
4. Network intrusion detection systems
5. Word processors
