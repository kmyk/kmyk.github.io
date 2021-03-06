---
layout: post
redirect_from:
  - /writeup/algo/codeforces/327-b/
  - /blog/2015/12/17/cf-327-b/
date: 2015-12-17T23:54:15+09:00
tags: [ "competitive", "writeup", "codeforces", "sieve-of-eratosthenes" ]
---

# Codeforces Round #191 (Div. 2) B. Hungry Sequence

## [B. Hungry Sequence](http://codeforces.com/contest/327/problem/B) {#b}

素数を昇順に$n$個出力すればよい。

``` c++
#include <cstdio>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
constexpr int max_prime = 1299709;
using namespace std;
vector<int> eratosthenes_sieve(int N) {
    vector<bool> is_prime(N+1, true);
    is_prime[0] = is_prime[1] = false;
    for (int i = 2; i*i <= N; ++i)
        if (is_prime[i])
            for (int k = i+i; k <= N; k += i)
                is_prime[k] = false;
    vector<int> primes;
    for (int i = 2; i <= N; ++i)
        if (is_prime[i])
            primes.push_back(i);
    return primes;
}
int main() {
    vector<int> ps = eratosthenes_sieve(max_prime);
    int n; scanf("%d", &n);
    repeat (i,n) {
        if (i) printf(" ");
        printf("%d", ps[i]);
    }
    printf("\n");
    return 0;
}
```

### 余談

rubyのprimeを叩いたらTLEした。

``` ruby
#!/usr/bin/env ruby
require 'prime'
n = gets.to_i
puts Prime.take(n).join(' ')
```
