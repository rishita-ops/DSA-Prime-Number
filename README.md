# DSA — Prime Number Checker

A straightforward C++ implementation that determines whether a given positive integer is **prime**, **composite**, or **neither** (the special case of 1). This problem is a foundational exercise in **divisibility logic**, **loop boundary optimization**, and **special case handling** — three things that appear together in countless number-theory problems in DSA and competitive programming.

---

## Problem Statement

Given a positive integer `n`, determine whether it is:
- **Prime** — divisible only by 1 and itself
- **Composite** — has at least one divisor other than 1 and itself
- **Neither** — the special case of `1`, which is formally excluded from both categories

**Example Input:**
```
Enter a positive integer: 17
```

**Example Output:**
```
17 is a prime number.
```

---

**Example Input:**
```
Enter a positive integer: 12
```

**Example Output:**
```
12 is not a prime number.
```

---

**Example Input:**
```
Enter a positive integer: 1
```

**Example Output:**
```
1 is neither prime nor composite.
```

---

## The Code

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n, i, flag = 0;
    cout << "Enter a positive integer: ";
    cin >> n;

    for (i = 2; i <= n / 2; ++i)
    {
        if (n % i == 0)
        {
            flag = 1;
            break;
        }
    }

    if (n == 1)
    {
        cout << "1 is neither prime nor composite.";
    }
    else
    {
        if (flag == 0)
            cout << n << " is a prime number.";
        else
            cout << n << " is not a prime number.";
    }

    return 0;
}
```

---

## How It Works

1. **Read input** — `n` is read from standard input.
2. **Trial division loop** — Every integer from `2` to `n/2` is tested as a potential divisor. If any `i` divides `n` evenly (`n % i == 0`), `flag` is set to `1` and the loop exits immediately.
3. **Special case for `1`** — Checked after the loop. `1` is neither prime nor composite by mathematical convention, and the code handles it explicitly.
4. **Output** — If `flag` is still `0` after the loop, no divisor was found — `n` is prime. Otherwise, it is composite.

---

## The `flag` Variable — An Integer Acting as a Boolean

The code uses `int flag = 0` as a two-state signal:

| `flag` value | Meaning |
|--------------|---------|
| `0`          | No divisor found — prime (so far) |
| `1`          | Divisor found — composite |

This is a classic **C-style boolean flag** pattern — entirely correct and functional. The modern C++ equivalent would be `bool flag = false`, which is more expressive and self-documenting. Both compile and behave identically in this context.

---

## Algorithm (Pseudocode)

```
read n
flag ← 0

for i from 2 to n/2 (inclusive):
    if n % i == 0:
        flag ← 1
        break

if n == 1:
    print "neither prime nor composite"
else if flag == 0:
    print n, "is prime"
else:
    print n, "is not prime"
```

---

## Why the Loop Runs to `n/2`

If `n` has a divisor `d` where `d > n/2`, then `n/d < 2`, meaning the corresponding co-divisor would be less than 2 — which can't be an integer divisor greater than 1. So **no prime factor of `n` can exceed `n/2`**, and the loop correctly stops there.

This is valid and correct. However, it is not the tightest possible bound.

### The Tighter Bound — `sqrt(n)`

A stronger mathematical property holds: **if `n` has any divisor greater than 1, it must have one ≤ `√n`**. This is because divisors come in pairs `(d, n/d)` — if both were greater than `√n`, their product would exceed `n`, a contradiction.

| Loop Bound | Iterations for `n = 1,000,000` | Correct? |
|------------|-------------------------------|----------|
| `n / 2`    | ~500,000                      | ✅ Yes   |
| `sqrt(n)`  | ~1,000                        | ✅ Yes   |

The `sqrt(n)` bound is **500× faster** for large inputs and is the standard in optimized implementations:

```cpp
for (i = 2; i * i <= n; ++i)   // equivalent to i <= sqrt(n), avoids floating-point
```

The current `n/2` implementation is pedagogically clear and entirely correct — knowing the `sqrt(n)` optimization is the natural next step.

---

## The `n == 1` Check — Placement Matters

The special case `if (n == 1)` is checked **after** the loop. For `n = 1`, the loop condition is `i <= 1/2 = 0`, which is false from the start — the loop body never executes. `flag` remains `0`.

Without the explicit `n == 1` check, the code would incorrectly print `"1 is a prime number"` since `flag` stayed `0`. The post-loop check correctly catches this — but it is worth noting that checking `n < 2` *before* the loop as an early exit would be cleaner and would also handle `n = 0` and negative inputs:

```cpp
if (n < 2) {
    cout << n << " is neither prime nor composite.";
    return 0;
}
```

---

## Dry Run

**Input:** `n = 29`

| `i` | `29 % i` | Divisible? |
|-----|----------|------------|
| 2   | 1        | ❌         |
| 3   | 2        | ❌         |
| 4   | 1        | ❌         |
| 5   | 4        | ❌         |
| 6   | 5        | ❌         |
| 7   | 1        | ❌         |
| 8   | 5        | ❌         |
| 9   | 2        | ❌         |
| 10  | 9        | ❌         |
| 11  | 7        | ❌         |
| 12  | 5        | ❌         |
| 13  | 3        | ❌         |
| 14  | 1        | ❌         |

*(Loop ends at `i <= 29/2 = 14`)*
**`flag` remains `0` → Output:** `29 is a prime number.` ✅

---

**Input:** `n = 18`

| `i` | `18 % i` | Divisible? |
|-----|----------|------------|
| 2   | 0        | ✅ → `flag = 1`, break |

**Output:** `18 is not a prime number.` ✅

---

## Complexity Analysis

| Metric | Complexity |
|--------|------------|
| Time   | **O(n)** — loop runs up to `n/2` iterations in the worst case (prime numbers) |
| Space  | **O(1)** — only a fixed number of integer variables used |

> **With `sqrt(n)` optimization:** Time complexity improves to **O(√n)** — a significant practical improvement for large values of `n`.

---

## Edge Cases

| Input | Loop Executes? | `flag` | Output |
|-------|---------------|--------|--------|
| `n = 1` | No (`i <= 0` is false) | 0 | "neither prime nor composite" |
| `n = 2` | No (`i <= 1` is false) | 0 | "2 is a prime number" ✅ |
| `n = 3` | No (`i <= 1` is false) | 0 | "3 is a prime number" ✅ |
| `n = 4` | Yes (`i=2`: 4%2=0) | 1 | "4 is not a prime number" ✅ |
| `n = 0` | No (`i <= 0` is false) | 0 | Incorrectly prints "0 is a prime number" ⚠️ |
| Negative `n` | No (loop skipped) | 0 | Incorrectly prints prime ⚠️ |

> **Unhandled inputs:** `n = 0` and negative integers are not validated. Both bypass the loop and the `n == 1` check, resulting in incorrect output. A guard like `if (n < 2)` before the loop resolves both cases.

---

## Known Primes for Reference

```
2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47,
53, 59, 61, 67, 71, 73, 79, 83, 89, 97
```
*(All primes below 100)*

> `2` is the only even prime. Every other even number is divisible by 2 and therefore composite — a useful early-exit condition in optimized versions.

---

## Repository Structure

```
DSA-Prime-Check/
│
├── prime_check.cpp     # Main C++ implementation
└── README.md           # Project documentation
```

---

## How to Compile and Run

**Prerequisites:** GCC / G++

```bash
# Clone the repository
git clone https://github.com/rishita-ops/DSA-Prime-Check.git
cd DSA-Prime-Check

# Compile
g++ prime_check.cpp -o prime_check

# Run
./prime_check
```

**On Windows:**
```bash
g++ prime_check.cpp -o prime_check.exe
prime_check.exe
```

---

## Key Concepts Covered

- **Trial division** — testing every candidate divisor from `2` to `n/2`
- **Early exit with `break`** — stops the loop the moment a divisor is found, avoiding redundant work
- **C-style integer flag** — using `int flag` as a two-state signal; contrast with modern `bool`
- **Loop boundary reasoning** — why `n/2` is correct, and why `sqrt(n)` is tighter
- **Special case handling** — `n = 1` as a mathematically distinct case from composites
- **Post-loop state analysis** — understanding what `flag = 0` means after the loop completes

---

## Why This Problem Matters in DSA

Prime checking is not a standalone problem — it is infrastructure:

| Problem / Concept | Connection |
|-------------------|------------|
| Sieve of Eratosthenes | Generates all primes up to `n` in O(n log log n) — the scalable extension of this idea |
| Prime Factorization | Built directly on trial division with the same loop structure |
| Goldbach's Conjecture problems | Requires fast prime checking across a range |
| Modular Inverse (Fermat's Little Theorem) | Valid only when the modulus is prime |
| RSA Cryptography | Security depends entirely on the difficulty of prime factorization of large numbers |
| LeetCode #204 (Count Primes) | Direct application — optimize this into a sieve |
| LeetCode #866 (Prime Palindrome) | Combines palindrome check with prime check |

Every number theory problem either uses primes directly or depends on properties that only hold for primes. This implementation is the correct starting point before moving to sieves and probabilistic primality tests.

---

## Contributing

Contributions are welcome. Consider adding:
- An **optimized version** using `i * i <= n` (sqrt bound) for large input performance
- A **Sieve of Eratosthenes** implementation for range-based prime generation
- Input validation for `n = 0` and negative integers
- Implementations in Python, Java, or JavaScript

```bash
git checkout -b feature/your-feature
git commit -m "Add: your feature description"
git push origin feature/your-feature
# Then open a Pull Request
```

---

## License

This project is open-source and available under the [MIT License](LICENSE).

---

*Part of a structured DSA practice series — fundamentals, done right.*
