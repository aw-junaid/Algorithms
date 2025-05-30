# Summations: Formulas, Properties, and Bounding Techniques

## Introduction to Summations

Summations are fundamental mathematical operations that involve adding a sequence of numbers. They are widely used in computer science, mathematics, and engineering for analyzing algorithms, calculating probabilities, and solving various computational problems.

## Summation Notation

The summation of a sequence is typically represented using sigma notation (Σ):

```
n
Σ f(i) = f(1) + f(2) + ... + f(n)
i=1
```

Where:
- Σ is the summation symbol
- i is the index of summation
- 1 is the lower bound
- n is the upper bound
- f(i) is the function being summed

## Common Summation Formulas

### 1. Constant Series

```
n
Σ c = c * n
i=1
```

### 2. Linear Series

```
n
Σ i = n(n+1)/2
i=1
```

### 3. Quadratic Series

```
n
Σ i² = n(n+1)(2n+1)/6
i=1
```

### 4. Cubic Series

```
n
Σ i³ = [n(n+1)/2]²
i=1
```

### 5. Geometric Series

```
n
Σ r^i = (r^(n+1) - 1)/(r - 1) for r ≠ 1
i=0
```

### 6. Harmonic Series

```
n
Σ 1/i ≈ ln(n) + γ (Euler-Mascheroni constant)
i=1
```

## Summation Properties

### 1. Linearity Property

```
n
Σ (a*f(i) + b*g(i)) = a*Σf(i) + b*Σg(i)
i=1
```

### 2. Splitting Property

```
n
Σ f(i) = Σ f(i) + Σ f(i) where 1 ≤ k ≤ n
i=1   i=1   i=k+1
```

### 3. Index Shifting

```
n
Σ f(i) = Σ f(j+1) where j = i-1
i=1   j=0
```

## Bounding Summations

Bounding summations is crucial for algorithm analysis, particularly in determining time complexity.

### 1. Bounding by Integrals

For monotonically increasing functions:
```
∫ f(x)dx from m-1 to n ≤ Σ f(i) from i=m to n ≤ ∫ f(x)dx from m to n+1
```

For monotonically decreasing functions:
```
∫ f(x)dx from m to n+1 ≤ Σ f(i) from i=m to n ≤ ∫ f(x)dx from m-1 to n
```

### 2. Approximation Methods

- **Asymptotic approximation**: Using Big-O notation to describe growth rates
- **Dominant term**: Focusing on the term with the highest growth rate
- **Stirling's approximation**: For factorials in sums

## C Implementation Examples

### 1. Sum of First n Natural Numbers

```c
#include <stdio.h>

int sum_natural_numbers(int n) {
    return n * (n + 1) / 2;
}

int main() {
    int n = 100;
    printf("Sum of first %d natural numbers: %d\n", n, sum_natural_numbers(n));
    return 0;
}
```

### 2. Sum of Squares

```c
#include <stdio.h>

int sum_of_squares(int n) {
    return n * (n + 1) * (2 * n + 1) / 6;
}

int main() {
    int n = 10;
    printf("Sum of squares of first %d numbers: %d\n", n, sum_of_squares(n));
    return 0;
}
```

### 3. Geometric Series Sum

```c
#include <stdio.h>
#include <math.h>

double geometric_series_sum(double r, int n) {
    if (r == 1.0) return n + 1;
    return (pow(r, n + 1) - 1) / (r - 1);
}

int main() {
    double r = 2.0;
    int n = 10;
    printf("Sum of geometric series with r=%.2f for %d terms: %.2f\n", 
           r, n, geometric_series_sum(r, n));
    return 0;
}
```

### 4. Bounding Summation Using Integration

```c
#include <stdio.h>
#include <math.h>

double lower_bound_log_sum(int n) {
    return log(n);
}

double upper_bound_log_sum(int n) {
    return log(n) + 1;
}

int main() {
    int n = 100;
    double actual_sum = 0.0;
    
    for (int i = 1; i <= n; i++) {
        actual_sum += 1.0 / i;
    }
    
    printf("Harmonic sum H_%d: %.5f\n", n, actual_sum);
    printf("Lower bound (ln(%d)): %.5f\n", n, lower_bound_log_sum(n));
    printf("Upper bound (ln(%d)+1): %.5f\n", n, upper_bound_log_sum(n));
    
    return 0;
}
```

---

