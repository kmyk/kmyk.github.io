---
layout: post
redirect_from:
  - /writeup/algo/codeforces/687-b/
  - /blog/2016/06/30/cf-687-b/
date: 2016-06-30T05:20:57+09:00
tags: [ "competitive", "writeup", "codeforces" ]
"target_url": [ "http://codeforces.com/contest/687/problem/B" ]
---

# Codeforces Round #360 (Div. 1) B. Remainders Game

`cin`はだめ。`scanf("%d", n);`とかで死んで以来`cin`/`cout`を使ってきたが、この機に`scanf`/`printf`派に戻る。入力取るだけでTLEるのでは何のためにc++を使ってるか分からない。

## problem

正整数$k$と$c_1, \dots c_n$が与えられる。
正整数$x$に対して、以下の述語を$\phi(x)$とする。

-   $x \bmod c_1, \dots, x \bmod c_n$が与えられたとき、この情報のみから$x \bmod k$の値が復元できる。

$\forall x \ge 1. \phi(x)$が成り立っているか答えよ。

## solution

Check whether $k \mid \operatorname{lcm} \\{ c_1, \dots, c_n \\}$ holds or not. $O(n \log c_i)$.

## implementation

`cin` causes TLE. Use `scanf`.

``` c++
#include <cstdio>
#include <vector>
#include <map>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
using namespace std;

vector<int> sieve_of_eratosthenes(int n) { // enumerate primes in [2,n] with O(n log log n)
    vector<bool> is_prime(n+1, true);
    is_prime[0] = is_prime[1] = false;
    for (int i = 2; i*i <= n; ++i)
        if (is_prime[i])
            for (int k = i+i; k <= n; k += i)
                is_prime[k] = false;
    vector<int> primes;
    for (int i = 2; i <= n; ++i)
        if (is_prime[i])
            primes.push_back(i);
    return primes;
}
map<int,int> factors(int n, vector<int> const & primes) {
    map<int,int> result;
    for (int p : primes) {
        if (n < p * p) break;
        while (n % p == 0) {
            result[p] += 1;
            n /= p;
        }
    }
    if (n != 1) result[n] += 1;
    return result;
}

const int sqrt_max = 1e3;
int main() {
    // prepare
    vector<int> primes = sieve_of_eratosthenes(sqrt_max);
    // input
    int n, k; scanf("%d%d", &n, &k);
    vector<int> c(n); repeat (i,n) scanf("%d", &c[i]);
    // solve
    map<int,int> ps = factors(k, primes);
    map<int,bool> satisfied;
    repeat (i,n) {
        map<int,int> qs = factors(c[i], primes);
        for (auto it : qs) {
            int q, cnt; tie(q, cnt) = it;
            if (ps.count(q) and ps[q] <= cnt) {
                satisfied[q] = true;
            }
        }
    }
    bool ans = true;
    for (auto it : ps) {
        int p, cnt; tie(p, cnt) = it;
        if (not satisfied[p]) {
            ans = false;
            break;
        }
    }
    // output
    puts(ans ? "Yes" : "No");
    return 0;
}
```
