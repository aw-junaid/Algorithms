# Polynomials and the FFT: A Comprehensive Guide

This guide covers polynomial representation, the Discrete Fourier Transform (DFT), Fast Fourier Transform (FFT), and efficient implementations, with C code examples.

## Table of Contents
1. [Representing Polynomials](#representing-polynomials)
2. [The DFT and FFT](#the-dft-and-fft)
3. [Efficient FFT Implementations](#efficient-fft-implementations)
4. [C Implementation](#c-implementation)

## Representing Polynomials

A polynomial of degree n-1 can be represented in several ways:

### Coefficient Representation
The most common form is the coefficient vector:
```
A(x) = a₀ + a₁x + a₂x² + ... + a_{n-1}x^{n-1}
```
represented as `[a₀, a₁, ..., a_{n-1}]`.

### Point-Value Representation
A polynomial of degree n-1 can also be represented by n point-value pairs:
```
{(x₀, y₀), (x₁, y₁), ..., (x_{n-1}, y_{n-1})}
```
where `y_k = A(x_k)`.

### Conversion Between Representations
- **Evaluation**: Coefficient → Point-value (compute A(x) at n points)
- **Interpolation**: Point-value → Coefficient (solve for coefficients given n points)

## The DFT and FFT

### Discrete Fourier Transform (DFT)
The DFT converts a coefficient vector to point-value form using special points - the complex roots of unity.

For a polynomial `A(x)` of degree n-1, the DFT evaluates A at the nth roots of unity:
```
ω_n^k = e^{2πik/n} for k = 0,1,...,n-1
```

The DFT is defined as:
```
y_k = A(ω_n^k) = Σ_{j=0}^{n-1} a_j ω_n^{kj} for k=0,1,...,n-1
```

### Fast Fourier Transform (FFT)
The FFT is an O(n log n) algorithm to compute the DFT by exploiting symmetry properties of roots of unity.

Key ideas:
1. **Divide-and-conquer**: Split even and odd coefficients
2. **Recursive computation**: Compute FFT of smaller polynomials
3. **Combination**: Use symmetry ω_n^{k+n/2} = -ω_n^k

## Efficient FFT Implementations

### Recursive FFT
Straightforward implementation of the divide-and-conquer approach.

### Iterative FFT
More efficient implementation that:
1. Avoids recursion overhead
2. Uses bit-reversal permutation
3. Performs in-place computation

### Optimizations
- Precompute roots of unity
- Use memory-efficient in-place computation
- Leverage SIMD instructions when available

## C Implementation

Here's a complete C implementation of the FFT:

```c
#include <stdio.h>
#include <math.h>
#include <complex.h>

#define PI 3.14159265358979323846

// Recursive FFT implementation
void fft_recursive(complex double* a, int n, complex double* out) {
    if (n == 1) {
        out[0] = a[0];
        return;
    }
    
    complex double even[n/2], odd[n/2];
    complex double even_out[n/2], odd_out[n/2];
    
    // Split even and odd coefficients
    for (int i = 0; i < n/2; i++) {
        even[i] = a[2*i];
        odd[i] = a[2*i + 1];
    }
    
    // Recursive calls
    fft_recursive(even, n/2, even_out);
    fft_recursive(odd, n/2, odd_out);
    
    // Combine results
    for (int k = 0; k < n/2; k++) {
        complex double t = cexp(-2 * PI * I * k / n) * odd_out[k];
        out[k] = even_out[k] + t;
        out[k + n/2] = even_out[k] - t;
    }
}

// Iterative FFT using bit-reversal permutation
void fft_iterative(complex double* a, int n, complex double* out) {
    // Bit-reversal permutation
    for (int i = 0; i < n; i++) {
        int rev = 0;
        for (int j = 1, k = n >> 1; j < n; j <<= 1, k >>= 1) {
            if (i & j) rev |= k;
        }
        out[i] = a[rev];
    }
    
    // Iterative FFT
    for (int len = 2; len <= n; len <<= 1) {
        double angle = -2 * PI / len;
        complex double wlen = cexp(angle * I);
        
        for (int i = 0; i < n; i += len) {
            complex double w = 1;
            for (int j = 0; j < len/2; j++) {
                complex double u = out[i + j];
                complex double v = w * out[i + j + len/2];
                out[i + j] = u + v;
                out[i + j + len/2] = u - v;
                w *= wlen;
            }
        }
    }
}

// Inverse FFT
void ifft(complex double* a, int n, complex double* out) {
    // Conjugate the input
    for (int i = 0; i < n; i++) {
        out[i] = conj(a[i]);
    }
    
    // Compute forward FFT
    complex double* temp = (complex double*)malloc(n * sizeof(complex double));
    fft_iterative(out, n, temp);
    
    // Conjugate and scale
    for (int i = 0; i < n; i++) {
        out[i] = conj(temp[i]) / n;
    }
    
    free(temp);
}

int main() {
    int n = 8;
    complex double a[] = {1, 2, 3, 4, 5, 6, 7, 8};
    complex double out[n];
    
    printf("Original polynomial coefficients:\n");
    for (int i = 0; i < n; i++) {
        printf("%.2f + %.2fi\n", creal(a[i]), cimag(a[i]));
    }
    
    // Compute FFT
    fft_iterative(a, n, out);
    
    printf("\nFFT result (point-value representation):\n");
    for (int i = 0; i < n; i++) {
        printf("%.2f + %.2fi\n", creal(out[i]), cimag(out[i]));
    }
    
    // Compute inverse FFT
    complex double reconstructed[n];
    ifft(out, n, reconstructed);
    
    printf("\nReconstructed coefficients:\n");
    for (int i = 0; i < n; i++) {
        printf("%.2f + %.2fi\n", creal(reconstructed[i]), cimag(reconstructed[i]));
    }
    
    return 0;
}
```

### Explanation of the C Code

1. **Recursive FFT**:
   - Base case handles n=1
   - Splits input into even and odd coefficients
   - Recursively computes FFT of smaller polynomials
   - Combines results using roots of unity

2. **Iterative FFT**:
   - First performs bit-reversal permutation
   - Processes the array in increasing powers of 2
   - Uses butterfly operations to combine results

3. **Inverse FFT**:
   - Uses the property that IFFT is similar to FFT with conjugation and scaling
   - Computes FFT of conjugated input
   - Conjugates and scales the result

4. **Main Function**:
   - Demonstrates FFT and IFFT on sample data
   - Shows reconstruction of original coefficients
---
