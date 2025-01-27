# Code Snippets for Data Structure and Algorithm

**Table of Contents**:

- [Code Snippets for Data Structure and Algorithm](#code-snippets-for-data-structure-and-algorithm)
  - [Number Theory](#number-theory)
    - [Prime Number Checking](#prime-number-checking)
    - [Sieve of Eratosthenes](#sieve-of-eratosthenes)
    - [Prime Factorization](#prime-factorization)
    - [Binary Exponentiation](#binary-exponentiation)
    - [Number of Divisors](#number-of-divisors)
    - [Number of Factors, Sum of Factors & Product of Factors](#number-of-factors-sum-of-factors--product-of-factors)
    - [Density of Primes](#density-of-primes)
    - [GCD and LCM](#gcd-and-lcm)
    - [Number of Co-primes in range - Euler Totient Function](#number-of-co-primes-in-range---euler-totient-function)
    - [Phi - Sieve](#phi---sieve)

## Number Theory

### Prime Number Checking

- **Wiki**: [Prime Number](https://en.wikipedia.org/wiki/Prime_number)
- **Blog**: [Primality Testing](https://www.rookieslab.com/posts/fastest-way-to-check-if-a-number-is-prime-or-not)

```c++
bool prime(int n) {
    if (n < 2) return false;
    if (n <= 3) return true;
    if (n % 2 == 0) return false;

    for (int i = 3; i * i <= n; i += 2) {
        if (n % i == 0) return false;
    }
    return true;
}
```

**Time Complexity**: `O(sqrt(n))`

### Sieve of Eratosthenes

- [Benchmark Testing for Prime Sieve](https://gist.github.com/jakobkogler/e6359ea9ced24fe304f1a8af3c9bee0e)

```c++
int n = 1e6+7;   // 1000007
vector <bool> is_prime(n+1, true); // additional 1 for boundary checking.

void sieve() {
    is_prime[0] = is_prime[1] = false;

    for (int i = 2; i * i <= n; i++) {
        if (is_prime[i]) {
            for (int j = i * i; j <= n; j += i) 
                is_prime[j] = false;
        }
    }
}
```

**Problems**:

- [230B(T Primes) - Codeforces](https://codeforces.com/problemset/problem/230/B)

```
Definition: A number that has exactly three distinct positive divisors is a T-prime number.

Solution Idea: It can be shown that only squares of prime numbers are T-primes, and that there are not too many of them — as many as there are prime numbers not greater than . Precompute these numbers (using, for example, the sieve of Eratosthenes) and store them in an array or an std::set, then we can answer each query by simply checking whether the number in question is amongst the precomputed numbers.

4 = 1, 2, 4      ; sqrt(4) = 2 is prime && 2 * 2 == 4 (whole square number)
9 = 1, 3, 9      ; sqrt(9) = 3 is prime && 3 * 3 == 9 (whole square number)
25 = 1, 5, 25
49 = 1, 7, 49 
```

### Prime Factorization

```c++
void printPrimeFactor(int n) {
    for (int i = 2; i * i <= n; i++) {
        int count = 0;
        if (n % i == 0) {
            while (n % i == 0) {
                count++;
                n /= i;
            }
            cout << i << " " << count << endl;
        }
    }

    if (n > 1) cout << n << " " << 1 << endl;
}
```

### Binary Exponentiation

```text
Suppose we want to calculate 2^9 = 512

Condition:
---------------------
If power % 2 == 1:
    res *= base;
    power--
Else:
    base *= base;
    power >>= 1

res     base        power
-----   ----        ------
1       2             9     (odd)
2       2             8     (even)
2       4             4     (even)
2       16            2     (even)
2       256           1     (odd)
512     256           0     break
```

```c++
ll power(ll base, ll power) {
    ll res = 1;

    while (power) {
        if (power % 2) {
            res *= base;
            power--;
        } else {
            base *= base;
            power /= 2;
        }
    }

    return res;
}
```

### Number of Divisors


```c++
void divisors(int n) {
    set<int> divs;
    
    for (int i = 1; i * i <= n; i++) {
        if (n % i == 0) {
            divs.insert(i);
            divs.insert(n/i);
        }
    }
    cout << divs.size() << endl;
}
```

- Solve leetcode [Four Divisors](https://leetcode.com/problems/four-divisors/) problem.

```text
Idea: 

    * If i * i == n; there are always odd number of divisors; so ignore this number;
    * First added 1 + n;
    * And find the next divisors. If you find more than or less than one. Then ignore that number.

```

- Leetcode [Three Divisors](https://leetcode.com/problems/three-divisors/)

```text
Idea:

    * If number is full square number then set the counter and make the decision.
```

- Leetcode [Self Dividing Number](https://leetcode.com/problems/self-dividing-numbers/)


### Number of Factors, Sum of Factors & Product of Factors

- Leetcode [Kth Divisor](https://leetcode.com/problems/the-kth-factor-of-n/)

```c++
class Solution {
public:
    int kthFactor(int n, int k) {
        for (int i = 1; i <= n / 2; i++) {
            if (n % i == 0) k--;
            if (k == 0) return i;
        }
        if (k == 1) return n;
        return -1;
    }
};
```

**Number of Factors**:

```text
Number of factors:

    For 84 = 2^2.3^1.7^1
    Increase each by 1.
    2 + 1 = 3
    1 + 1 = 2
    1 + 1 = 2
---------------------
    Multiply every (3 * 2 * 2) => 12
```

```c++
int nFactors(int n) {
    int nfactors = 1;
    for (int i = 2; i * i <= n; i++) {
        int count = 0;

        if (n % i == 0) {
            while (n % i == 0) {
                n /= 2;
                count++;
            }
        }
        nfactors *= (count + 1);
    }

    // for prime number
    if (n > 1) nfactors *= 2;

    return nfactors;
}
```

**Sum of Factors**:

```c++
int sumOfFactors(int n) {
    int sum = 1;

    for (int i = 2; i * i <= n; i++) {
        int counter = 0;
        if (n % i == 0) {
            while (n % i == 0) {
                n /= i;
                counter++;
            }

            // (i ^ (counter + 1) - 1) / (i - 1)
            // cout << sum << " " << i << " " << counter << endl;
            sum *= (pow(i, counter + 1) - 1) / (i - 1);
        }
    }

    if (n > 1) sum *= (pow(n, 2) - 1) / (n - 1);

    return sum;
}
```

- [Perfect Numbers](https://en.wikipedia.org/wiki/List_of_Mersenne_primes_and_perfect_numbers)

```text
    A number n is called perfect number if n = sum_of_factors(n) - n.
```

**Product of Factors**:

```c++
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

int numOfFactors(int n) {
    int factors = 1;

    for (int i = 2; i * i <= n; i++) {
        int counter = 0;
        
        if (n % i == 0) {
            while (n % i == 0) {
                n /= i;
                counter++;
            }
        }
        factors *= (counter + 1);
    }

    if (n > 1) factors *= 2;

    return factors;
}


ll binExp(ll base, ll power) {
    ll ans = 1;

    while (power) {
        if (power % 2) {
            ans *= base;
            power--;
        } else {
            base *= base;
            power /= 2;
        }
    }

    return ans;
}

ll prodOfFactors(ll n) {
    ll sf = numOfFactors(n);
    sf /= 2;

    ll prod = binExp(n, sf);

    return prod;
}

int main() {
    cout << prodOfFactors(84) << endl;  // 351298031616
}
```

### Density of Primes

```text
    The density of primes are relatively $(n) = n / ln(n);
```

```c++
int n = 10e5;

// method = n / ln(n)
int density = n / log(n);   // log() in c++ is natural logarithm. e-based.
```

### GCD and LCM

```c++
int gcd(int a, int b) {
    if (b == 0) return a;
    return gcd(b, a % b);
}
```

```c++
int lcm(int a, int b) {
    int _lcm = (a * b) / gcd(a, b);
    return _lcm;
}
```

### Number of Co-primes in range - Euler Totient Function

- [Euler Totient - CP Algorithms](https://cp-algorithms.com/algebra/phi-function.html#properties)

![images](images/1.png)

Here,

1. p1, p2, ..., pk is the prime factors.
2. We can easily generate prime factors and through prime factors we can easily generate
   co-primes using Euluer Totient function.

```c++
int phi(int n) {
    int coprimes = n;
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) {
            while (n % i == 0) n /= i;
            coprimes *= (i - 1);
            coprimes /= i;
        }
    }
    if (n > 1) {
        coprimes *= (n - 1);
        coprimes /= n;
    }

    return coprimes;
}
```

Another Implementation of `Phi()` Function

```c++
int phi(int n) {
    int result = n;
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) {
            while (n % i == 0)
                n /= i;
            result -= result / i;
        }
    }
    if (n > 1)
        result -= result / n;
    return result;
}
```

### Phi - Sieve 

```c++
void phi_1_to_n(int n) {
    vector<int> phi(n + 1);
    // store max value
    for (int i = 0; i <= n; i++)
        phi[i] = i;

    // subtract 
    for (int i = 2; i <= n; i++) {
        if (phi[i] == i) {
            for (int j = i; j <= n; j += i)
                phi[j] -= phi[j] / i;
        }
    }
}
```