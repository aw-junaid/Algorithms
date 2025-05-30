# Naive String-Matching Algorithm: Detailed Explanation and Implementation

The naive string-matching algorithm is a simple and straightforward method for finding all occurrences of a pattern within a text. It checks all possible positions in the text where the pattern could match.

## How the Naive Algorithm Works

1. **Concept**: The algorithm slides the pattern over the text one by one and checks for a match at every position.
2. **Process**:
   - Start with the first character of the text
   - Compare it with the first character of the pattern
   - If they match, compare the next characters of both
   - If all characters match, record the position
   - Move to the next character in the text and repeat

3. **Time Complexity**:
   - Worst-case: O((n-m+1)*m) where n is text length and m is pattern length
   - Best-case: O(n) when the pattern doesn't appear at all in the text

4. **Space Complexity**: O(1) - it doesn't require additional space

## Mathematical Representation

For WordPress, you can represent the algorithm's operation with this equation:

```
for s = 0 to n - m
    if P[1..m] == T[s+1..s+m]
        print "Pattern occurs at shift" s
```

Where:
- `P` is the pattern of length `m`
- `T` is the text of length `n`
- `s` is the shift (starting position in the text)

## C Implementation

Here's a complete implementation of the naive string-matching algorithm in C:

```c
#include <stdio.h>
#include <string.h>

void naiveStringMatch(char* text, char* pattern) {
    int textLength = strlen(text);
    int patternLength = strlen(pattern);
    
    // Slide the pattern over the text one by one
    for (int i = 0; i <= textLength - patternLength; i++) {
        int j;
        
        // For current index i, check for pattern match
        for (j = 0; j < patternLength; j++) {
            if (text[i + j] != pattern[j])
                break;
        }
        
        // If pattern matches at current position
        if (j == patternLength) {
            printf("Pattern found at index %d\n", i);
        }
    }
}

int main() {
    char text[1000];
    char pattern[100];
    
    printf("Enter the text: ");
    fgets(text, sizeof(text), stdin);
    text[strcspn(text, "\n")] = '\0'; // Remove trailing newline
    
    printf("Enter the pattern to search: ");
    fgets(pattern, sizeof(pattern), stdin);
    pattern[strcspn(pattern, "\n")] = '\0'; // Remove trailing newline
    
    naiveStringMatch(text, pattern);
    
    return 0;
}
```

## Explanation of the C Code

1. **Function `naiveStringMatch`**:
   - Takes text and pattern as input
   - Calculates lengths of both strings
   - Outer loop slides the pattern over the text
   - Inner loop checks for character-by-character match
   - Prints positions where pattern is found

2. **Main function**:
   - Reads input text and pattern from user
   - Removes newline characters from input
   - Calls the matching function

## Advantages and Disadvantages

**Advantages**:
- Simple to understand and implement
- No preprocessing required
- No additional space needed

**Disadvantages**:
- Inefficient for large texts (O(n*m) in worst case)
- Repeated comparisons of same characters
- Not suitable for applications requiring high performance

## When to Use

The naive algorithm is suitable for:
- Small texts and patterns
- Educational purposes to understand string matching
- Situations where implementation simplicity is more important than performance

For WordPress implementation, you can use the mathematical notation provided above, which will render properly with LaTeX plugins.
