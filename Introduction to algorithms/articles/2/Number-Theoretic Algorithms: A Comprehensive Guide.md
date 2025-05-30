# Number-Theoretic Algorithms: A Comprehensive Guide

This guide covers fundamental number-theoretic concepts and algorithms, with implementations in C. I'll explain each topic in detail and provide code examples that can be easily integrated into WordPress sites.

## Table of Contents
1. [Elementary Number-Theoretic Notions](#elementary-number-theoretic-notions)
2. [Greatest Common Divisor](#greatest-common-divisor)
3. [Modular Arithmetic](#modular-arithmetic)
4. [Solving Modular Linear Equations](#solving-modular-linear-equations)
5. [The Chinese Remainder Theorem](#the-chinese-remainder-theorem)
6. [Powers of an Element](#powers-of-an-element)
7. [The RSA Public-Key Cryptosystem](#the-rsa-public-key-cryptosystem)
8. [Primality Testing](#primality-testing)
9. [Integer Factorization](#integer-factorization)

## Elementary Number-Theoretic Notions

Number theory is the study of integers and their properties. Key concepts include:

- **Divisibility**: a divides b (a|b) if b = k*a for some integer k
- **Prime numbers**: Integers >1 divisible only by 1 and themselves
- **Congruence**: a ≡ b mod m if m divides (a-b)

```c
#include <stdio.h>

// Check if a divides b
int divides(int a, int b) {
    return b % a == 0;
}

// Check if two numbers are congruent modulo m
int congruent(int a, int b, int m) {
    return (a - b) % m == 0;
}
```

## Greatest Common Divisor

The GCD of two integers is the largest integer that divides both.

### Euclidean Algorithm

The efficient method to compute GCD:

```
gcd(a, b) = gcd(b, a mod b)
```

```c
// Recursive implementation
int gcd(int a, int b) {
    if (b == 0) return a;
    return gcd(b, a % b);
}

// Iterative implementation
int gcd_iterative(int a, int b) {
    while (b != 0) {
        int temp = b;
        b = a % b;
        a = temp;
    }
    return a;
}

// Extended Euclidean Algorithm (also finds coefficients x, y)
int extended_gcd(int a, int b, int *x, int *y) {
    if (b == 0) {
        *x = 1;
        *y = 0;
        return a;
    }
    int x1, y1;
    int gcd = extended_gcd(b, a % b, &x1, &y1);
    *x = y1;
    *y = x1 - (a / b) * y1;
    return gcd;
}
```

## Modular Arithmetic

Arithmetic operations performed on integers modulo m.

Key properties:
- (a + b) mod m = [(a mod m) + (b mod m)] mod m
- (a * b) mod m = [(a mod m) * (b mod m)] mod m

```c
// Modular addition
int mod_add(int a, int b, int m) {
    return ((a % m) + (b % m)) % m;
}

// Modular subtraction
int mod_sub(int a, int b, int m) {
    return ((a % m) - (b % m) + m) % m;
}

// Modular multiplication
int mod_mul(int a, int b, int m) {
    return ((a % m) * (b % m)) % m;
}

// Modular exponentiation (fast exponentiation)
int mod_pow(int base, int exp, int m) {
    int result = 1;
    base = base % m;
    while (exp > 0) {
        if (exp % 2 == 1)
            result = (result * base) % m;
        base = (base * base) % m;
        exp = exp / 2;
    }
    return result;
}
```

## Solving Modular Linear Equations

To solve equations of the form: ax ≡ b (mod m)

Solution exists if gcd(a,m) divides b.

```c
// Solves ax ≡ b (mod m) for x
int solve_linear_congruence(int a, int b, int m, int* solutions) {
    int x, y;
    int g = extended_gcd(a, m, &x, &y);
    
    if (b % g != 0) return 0; // No solution
    
    int x0 = (x * (b / g)) % m;
    if (x0 < 0) x0 += m;
    
    // Generate all solutions
    for (int i = 0; i < g; i++) {
        solutions[i] = (x0 + i * (m / g)) % m;
    }
    
    return g; // Number of solutions
}
```

## The Chinese Remainder Theorem

CRT states that for pairwise coprime moduli m₁, m₂, ..., mₙ, the system:
x ≡ a₁ mod m₁
x ≡ a₂ mod m₂
...
x ≡ aₙ mod mₙ
has a unique solution modulo M = m₁*m₂*...*mₙ

```c
// Chinese Remainder Theorem implementation
int chinese_remainder(int *a, int *m, int len) {
    int M = 1;
    for (int i = 0; i < len; i++) {
        M *= m[i];
    }
    
    int result = 0;
    for (int i = 0; i < len; i++) {
        int Mi = M / m[i];
        int x, y;
        extended_gcd(Mi, m[i], &x, &y);
        result += a[i] * Mi * x;
    }
    
    return result % M;
}
```

## Powers of an Element

Computing powers efficiently using exponentiation by squaring.

### Modular Exponentiation

Already shown in the modular arithmetic section (`mod_pow` function).

### Finding Primitive Roots

A primitive root modulo n is a number g whose powers generate all numbers coprime to n.

```c
// Check if g is a primitive root modulo n
int is_primitive_root(int g, int n) {
    if (gcd(g, n) != 1) return 0;
    
    int phi = euler_phi(n);
    int factors[100], exp[100];
    int count = factorize(phi, factors, exp);
    
    for (int i = 0; i < count; i++) {
        if (mod_pow(g, phi / factors[i], n) == 1)
            return 0;
    }
    return 1;
}
```

## The RSA Public-Key Cryptosystem

RSA steps:
1. Choose two large primes p and q
2. Compute n = p*q and φ(n) = (p-1)*(q-1)
3. Choose e such that 1 < e < φ(n) and gcd(e, φ(n)) = 1
4. Compute d ≡ e⁻¹ mod φ(n)
5. Public key: (n, e), Private key: (n, d)

```c
// RSA key generation
void rsa_key_gen(int p, int q, int *n, int *e, int *d) {
    *n = p * q;
    int phi = (p - 1) * (q - 1);
    
    // Choose e (typically 65537)
    *e = 65537;
    while (gcd(*e, phi) != 1) {
        (*e)++;
    }
    
    // Compute d
    int x, y;
    extended_gcd(*e, phi, d, &y);
    if (*d < 0) *d += phi;
}

// RSA encryption
int rsa_encrypt(int message, int e, int n) {
    return mod_pow(message, e, n);
}

// RSA decryption
int rsa_decrypt(int ciphertext, int d, int n) {
    return mod_pow(ciphertext, d, n);
}
```

## Primality Testing

### Miller-Rabin Primality Test

A probabilistic test to determine if a number is probably prime.

```c
// Miller-Rabin primality test
int is_prime(int n, int k) {
    if (n <= 1) return 0;
    if (n <= 3) return 1;
    if (n % 2 == 0) return 0;
    
    // Write n-1 as d*2^s
    int d = n - 1;
    int s = 0;
    while (d % 2 == 0) {
        d /= 2;
        s++;
    }
    
    for (int i = 0; i < k; i++) {
        int a = 2 + rand() % (n - 3);
        int x = mod_pow(a, d, n);
        
        if (x == 1 || x == n - 1) continue;
        
        int composite = 1;
        for (int j = 0; j < s - 1; j++) {
            x = mod_pow(x, 2, n);
            if (x == n - 1) {
                composite = 0;
                break;
            }
        }
        if (composite) return 0;
    }
    return 1;
}
```

## Integer Factorization

### Pollard's Rho Algorithm

An efficient algorithm for factoring composite numbers.

```c
// Pollard's Rho factorization
int pollards_rho(int n) {
    if (n % 2 == 0) return 2;
    if (n % 3 == 0) return 3;
    if (n % 5 == 0) return 5;
    
    while (1) {
        int c = 1 + rand() % (n - 1);
        int f = function(x, c, n) { return (mod_pow(x, 2, n) + c) % n; };
        
        int x = 2, y = 2, d = 1;
        while (d == 1) {
            x = f(x);
            y = f(f(y));
            d = gcd(abs(x - y), n);
        }
        if (d != n) return d;
    }
}

// Complete factorization
void factorize(int n, int *factors, int *exponents) {
    int count = 0;
    // Handle 2 separately
    while (n % 2 == 0) {
        if (count == 0 || factors[count-1] != 2) {
            factors[count] = 2;
            exponents[count] = 1;
            count++;
        } else {
            exponents[count-1]++;
        }
        n /= 2;
    }
    
    // Check odd divisors up to sqrt(n)
    for (int i = 3; i*i <= n; i += 2) {
        while (n % i == 0) {
            if (count == 0 || factors[count-1] != i) {
                factors[count] = i;
                exponents[count] = 1;
                count++;
            } else {
                exponents[count-1]++;
            }
            n /= i;
        }
    }
    
    // If remaining n is prime
    if (n > 1) {
        factors[count] = n;
        exponents[count] = 1;
        count++;
    }
}
```

## Helper Functions

These functions are used in some of the implementations above:

```c
// Euler's totient function
int euler_phi(int n) {
    int result = n;
    for (int p = 2; p * p <= n; p++) {
        if (n % p == 0) {
            while (n % p == 0)
                n /= p;
            result -= result / p;
        }
    }
    if (n > 1)
        result -= result / n;
    return result;
}
```

---

