# String Matching with Finite Automata

String matching with finite automata is an efficient algorithm for finding all occurrences of a pattern in a text. This method uses concepts from automata theory to preprocess the pattern and then efficiently scan the text in linear time.

## How Finite Automata String Matching Works

### Basic Concepts

1. **Finite Automaton (FA)**: A mathematical model consisting of:
   - A finite set of states (Q)
   - A start state (q₀)
   - A set of accepting states (F)
   - A transition function (δ) that moves between states based on input characters

2. **For string matching**:
   - The automaton is constructed from the pattern
   - The text is processed through the automaton
   - A match is found whenever the automaton reaches an accepting state

### Construction of the String Matching Automaton

For a given pattern P of length m, we construct an automaton with m+1 states (0 to m):

1. **States**: Each state q represents the length of the current match (0 ≤ q ≤ m)
2. **Accepting state**: State m (complete pattern matched)
3. **Transition function δ(q, a)**:
   - Computes the longest prefix of P that is a suffix of P[0..q-1]a
   - This ensures we don't miss potential matches when a partial match fails

### Mathematical Formulation

The transition function δ can be defined using the suffix function σ:

σ(x) = max{k : P[0..k-1] is a suffix of x}

Then:
δ(q, a) = σ(P[0..q-1]a)

For WordPress compatibility, here's the equation in simple text format:
δ(q, a) = length of the longest prefix of P that is also a suffix of P[0..q-1]a

### Algorithm Steps

1. **Preprocessing**: Build the transition table for the automaton (compute FA table)
2. **Matching**: Use the automaton to process the text

## C Implementation

Here's a complete implementation in C:

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define NO_OF_CHARS 256

// Function to compute the Finite Automaton transition function
void computeTransitionFunction(char *pattern, int m, int TF[][NO_OF_CHARS]) {
    int state, x;
    char a;
    
    for (state = 0; state <= m; ++state) {
        for (a = 0; a < NO_OF_CHARS; ++a) {
            TF[state][a] = getNextState(pattern, m, state, a);
        }
    }
}

// Function to get the next state
int getNextState(char *pattern, int m, int state, int a) {
    // If the character matches the next in pattern, move forward
    if (state < m && a == pattern[state]) {
        return state + 1;
    }
    
    // Otherwise find the longest prefix which is also suffix
    int ns, i;
    for (ns = state; ns > 0; ns--) {
        if (pattern[ns-1] == a) {
            for (i = 0; i < ns-1; i++) {
                if (pattern[i] != pattern[state-ns+1+i]) {
                    break;
                }
            }
            if (i == ns-1) {
                return ns;
            }
        }
    }
    
    return 0;
}

// Function to search for pattern in text using FA
void searchUsingFA(char *text, char *pattern) {
    int m = strlen(pattern);
    int n = strlen(text);
    
    // Create transition table
    int TF[m+1][NO_OF_CHARS];
    computeTransitionFunction(pattern, m, TF);
    
    // Process the text through the FA
    int i, state = 0;
    for (i = 0; i < n; i++) {
        state = TF[state][(int)text[i]];
        if (state == m) {
            printf("Pattern found at index %d\n", i - m + 1);
        }
    }
}

int main() {
    char text[] = "AABAACAADAABAAABAA";
    char pattern[] = "AABA";
    
    printf("Text: %s\n", text);
    printf("Pattern: %s\n\n", pattern);
    printf("Pattern occurrences:\n");
    
    searchUsingFA(text, pattern);
    
    return 0;
}
```

## Time and Space Complexity

- **Preprocessing time**: O(m^3 * |Σ|) where |Σ| is alphabet size (can be improved to O(m * |Σ|) with efficient implementations)
- **Matching time**: O(n) where n is text length
- **Space complexity**: O(m * |Σ|) for the transition table

## Advantages

1. Processes the text in a single pass
2. Each text character is examined exactly once
3. Works well for multiple pattern searches (with modifications)

## Disadvantages

1. High preprocessing time for long patterns
2. Large memory requirement for big alphabets

---

