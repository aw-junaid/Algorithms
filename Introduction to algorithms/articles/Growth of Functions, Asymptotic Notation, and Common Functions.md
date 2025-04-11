# **Growth of Functions, Asymptotic Notation, and Common Functions**

In algorithm analysis, understanding how functions grow and comparing their efficiency is crucial. This section covers:

1. **Growth of Functions**  
2. **Asymptotic Notations** (Big-O, Omega, Theta)  
3. **Standard Notations & Common Functions**  

---

## **1. Growth of Functions**
The **growth rate** of a function describes how its output increases as the input size (`n`) grows. This helps compare algorithms' efficiency.

### **Common Growth Rates (Fastest to Slowest)**
| Notation      | Name               | Example Algorithms                |
|--------------|--------------------|----------------------------------|
| $\(O(1)\)$     | Constant           | Array access, arithmetic ops.    |
| $\(O(\log n)\)$| Logarithmic        | Binary search                    |
| $\(O(n)\)$     | Linear             | Linear search, simple loops      |
| $\(O(n \log n)\)$| Linearithmic     | Merge sort, quicksort            |
| $\(O(n^2)\)$   | Quadratic          | Bubble sort, insertion sort      |
| $\(O(n^3)\)$   | Cubic              | Naive matrix multiplication      |
| $\(O(2^n)\)$   | Exponential        | Brute-force (subset generation)  |
| $\(O(n!)\)$    | Factorial          | Traveling Salesman (naive)       |

### **Why Growth Rates Matter**
- **Faster-growing functions** = Less efficient for large inputs.  
- **Slower-growing functions** = Preferred (e.g., $\(O(n \log n)\)$ is better than $\(O(n^2)\))$.  

---

## **2. Asymptotic Notations**
Asymptotic notations describe **upper bounds**, **lower bounds**, and **tight bounds** of a function.

### **(1) Big-O Notation $(\(O\))$ – Upper Bound**
- **Definition:** $\(f(n) = O(g(n))\) if \(f(n) \leq c \cdot g(n)\) for some \(c > 0\) and \(n \geq n_0\)$.  
- **Meaning:** "**Worst-case** growth rate."  
- **Example:**  
  - $\(3n^2 + 2n + 1 = O(n^2)\) (since \(3n^2\) dominates for large \(n\))$.  

### **(2) Omega Notation $(\(\Omega\))$ – Lower Bound**
- **Definition:** $\(f(n) = \Omega(g(n))\) if \(f(n) \geq c \cdot g(n)\) for some \(c > 0\) and \(n \geq n_0\)$.  
- **Meaning:** "**Best-case** growth rate."  
- **Example:**  
  - $\(n^2 = \Omega(n)\) (since \(n^2\)$ grows at least as fast as $\(n\))$.  

### **(3) Theta Notation $(\(\Theta\))$ – Tight Bound**
- **Definition:** $\(f(n) = \Theta(g(n))\)$ if $\(f(n) = O(g(n))\)$ **and** $\(f(n) = \Omega(g(n))\)$.  
- **Meaning:** "**Exact** growth rate."  
- **Example:**  
  - $\(2n^2 + 3n + 1 = \Theta(n^2)\)$ (bounded above and below by $\(n^2\))$.  

### **Comparison Summary**
| Notation   | Mathematical Meaning          | Use Case                     |
|------------|-------------------------------|-----------------------------|
| $\(O\)$      | $\(f(n) \leq c \cdot g(n)\)$    | **Worst-case** analysis     |
| $\(\Omega\)$ | $\(f(n) \geq c \cdot g(n)\)$    | **Best-case** analysis      |
| $\(\Theta\)$ | $\(c_1 g(n) \leq f(n) \leq c_2 g(n)\)$ | **Average-case** analysis  |

---

## **3. Standard Notations & Common Functions**
### **(1) Monotonic Functions**
- **Increasing:** $\(f(n+1) > f(n)\)$ (e.g., $\(n^2, 2^n\))$.  
- **Decreasing:** $\(f(n+1) < f(n)\)$ (e.g., $\(1/n\)$).  

### **(2) Polynomial vs. Exponential**
- **Polynomial:** $\(n^c\)$ (e.g., $\(n^2, n^3\)$).  
- **Exponential:** $\(c^n\)$ (e.g., $\(2^n, 3^n\)$).  
- **Exponential grows much faster!**  

### **(3) Logarithmic Functions**
- **Rule:** $\(\log_b n = \frac{\log_k n}{\log_k b}\)$ (change of base).  
- **Properties:**  
  - $\(\log(ab) = \log a + \log b\)$ 
  - $\(\log(a^b) = b \log a\)$  

### **(4) Factorials (\(n!\))**
- **Stirling’s Approximation:** $\(n! \approx \sqrt{2 \pi n} \left( \frac{n}{e} \right)^n\)$.  
- **Growth:** Faster than exponential but slower than $\(n^n\)$.  

---

## **Key Takeaways**
1. **Big-O** = Worst case, **Omega** = Best case, **Theta** = Tight bound.  
2. **Efficient algorithms** have slower-growing functions (e.g., $\(O(n \log n)\)$ vs. $\(O(n^2)\))$.  
3. **Exponential $(\(2^n)\)$ is impractical for large $\(n\)$**; prefer polynomial or logarithmic.  

---

### **Example Problem**
**Q:** Prove that $\(2n^2 + 3n + 1 = \Theta(n^2)\)$.  
**Solution:**  
- Find $\(c_1, c_2, n_0\)$ such that $\(c_1 n^2 \leq 2n^2 + 3n + 1 \leq c_2 n^2\)$.  
- For $\(n \geq 1\)$:  
  - Lower bound: $\(2n^2 \leq 2n^2 + 3n + 1 \Rightarrow c_1 = 2\)$.  
  - Upper bound: $\(2n^2 + 3n + 1 \leq 6n^2 \Rightarrow c_2 = 6\)$.  
- Thus, $\(2n^2 + 3n + 1 = \Theta(n^2)\)$.  

---

