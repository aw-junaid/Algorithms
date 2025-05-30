# Counting and Probability: A Comprehensive Guide

This guide covers the fundamentals of counting principles, probability theory, discrete random variables, and specific distributions (geometric and binomial), along with their tail behaviors. I'll explain each concept in detail and provide C implementations where applicable.

## 1. Counting Principles

### Fundamental Counting Principle
If one event can occur in *m* ways and a second can occur independently in *n* ways, the two events can occur in *m × n* ways.

### Permutations
Arrangements of items where order matters:
- **Permutations of n items**: n! = n × (n-1) × ... × 1
- **Permutations of n items taken r at a time**: P(n,r) = n!/(n-r)!

### Combinations
Selections of items where order doesn't matter:
- **Combinations of n items taken r at a time**: C(n,r) = n!/(r!(n-r)!)

```c
#include <stdio.h>

// Function to calculate factorial
long factorial(int n) {
    if (n == 0) return 1;
    return n * factorial(n - 1);
}

// Function to calculate permutations P(n,r)
long permutation(int n, int r) {
    return factorial(n) / factorial(n - r);
}

// Function to calculate combinations C(n,r)
long combination(int n, int r) {
    return factorial(n) / (factorial(r) * factorial(n - r));
}

int main() {
    int n = 5, r = 3;
    printf("P(%d,%d) = %ld\n", n, r, permutation(n, r));
    printf("C(%d,%d) = %ld\n", n, r, combination(n, r));
    return 0;
}
```

## 2. Probability Basics

Probability measures the likelihood of an event occurring:
- P(E) = Number of favorable outcomes / Total number of possible outcomes
- 0 ≤ P(E) ≤ 1

### Key Rules:
1. **Complement Rule**: P(E') = 1 - P(E)
2. **Addition Rule**: P(A ∪ B) = P(A) + P(B) - P(A ∩ B)
3. **Multiplication Rule**: P(A ∩ B) = P(A) × P(B|A)

## 3. Discrete Random Variables

A variable that takes on countable values with associated probabilities.

### Properties:
- **Probability Mass Function (PMF)**: p(x) = P(X = x)
- **Cumulative Distribution Function (CDF)**: F(x) = P(X ≤ x)
- **Expected Value**: E[X] = Σ [x × p(x)]
- **Variance**: Var(X) = E[X²] - (E[X])²

```c
#include <stdio.h>

// Example of calculating expected value and variance
void discrete_stats(double values[], double probs[], int n) {
    double expect = 0.0, expect_sq = 0.0;
    
    for (int i = 0; i < n; i++) {
        expect += values[i] * probs[i];
        expect_sq += values[i] * values[i] * probs[i];
    }
    
    double variance = expect_sq - expect * expect;
    
    printf("Expected Value: %.2f\n", expect);
    printf("Variance: %.2f\n", variance);
}

int main() {
    double values[] = {1, 2, 3, 4};
    double probs[] = {0.1, 0.2, 0.3, 0.4};
    int n = sizeof(values)/sizeof(values[0]);
    
    discrete_stats(values, probs, n);
    return 0;
}
```

## 4. Geometric Distribution

Models the number of trials until the first success in Bernoulli trials.

### PMF:
`P(X = k) = (1-p)^(k-1) × p` for k = 1, 2, 3,...

### CDF:
`P(X ≤ k) = 1 - (1-p)^k`

### Expected Value and Variance:
- E[X] = 1/p
- Var(X) = (1-p)/p²

```c
#include <stdio.h>
#include <math.h>

double geometric_pmf(double p, int k) {
    return pow(1-p, k-1) * p;
}

double geometric_cdf(double p, int k) {
    return 1 - pow(1-p, k);
}

int main() {
    double p = 0.2;
    int k = 3;
    
    printf("PMF at %d: %.4f\n", k, geometric_pmf(p, k));
    printf("CDF at %d: %.4f\n", k, geometric_cdf(p, k));
    return 0;
}
```

## 5. Binomial Distribution

Models the number of successes in n independent Bernoulli trials.

### PMF:
`P(X = k) = C(n,k) × p^k × (1-p)^(n-k)` for k = 0, 1, ..., n

### Expected Value and Variance:
- E[X] = n × p
- Var(X) = n × p × (1-p)

```c
#include <stdio.h>
#include <math.h>

long combination(int n, int k); // As defined earlier

double binomial_pmf(int n, double p, int k) {
    return combination(n, k) * pow(p, k) * pow(1-p, n-k);
}

double binomial_cdf(int n, double p, int k) {
    double sum = 0.0;
    for (int i = 0; i <= k; i++) {
        sum += binomial_pmf(n, p, i);
    }
    return sum;
}

int main() {
    int n = 10, k = 3;
    double p = 0.5;
    
    printf("PMF at %d: %.4f\n", k, binomial_pmf(n, p, k));
    printf("CDF at %d: %.4f\n", k, binomial_cdf(n, p, k));
    return 0;
}
```

## 6. Tails of the Binomial Distribution

### Right Tail (Upper Tail):
`P(X ≥ k) = 1 - P(X ≤ k-1) = 1 - CDF(k-1)`

### Left Tail (Lower Tail):
`P(X ≤ k) = CDF(k)`

### Approximations:
For large n, the binomial can be approximated by:
1. **Normal approximation**: N(np, np(1-p))
2. **Poisson approximation**: Pois(np) when p is small

```c
#include <stdio.h>
#include <math.h>

double binomial_right_tail(int n, double p, int k) {
    return 1 - binomial_cdf(n, p, k-1);
}

double binomial_left_tail(int n, double p, int k) {
    return binomial_cdf(n, p, k);
}

int main() {
    int n = 20, k = 5;
    double p = 0.3;
    
    printf("Right tail P(X >= %d): %.4f\n", k, binomial_right_tail(n, p, k));
    printf("Left tail P(X <= %d): %.4f\n", k, binomial_left_tail(n, p, k));
    return 0;
}
```

---

