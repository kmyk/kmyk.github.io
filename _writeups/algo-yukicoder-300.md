---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/300/
  - /blog/2016/07/07/yuki-300/
date: "2016-07-07T23:23:08+09:00"
tags: [ "competitive", "writeup", "yukicoder", "prime", "factorization" ]
"target_url": [ "http://yukicoder.me/problems/no/300" ]
---

# Yukicoder No.300 平方数

## solution

素因数分解して再構成。$O(\sqrt{n} \log \log n)$。

$x = {p_1}^{k_1} {p_2}^{k_2} \dots {p_n}^{k_n}$と素因数分解できたとき、
$xy = z^2$ for some $z$となるような最小の$y = \Pi \\{ p_i \mid k_i \text{ is odd} \\}$である。

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <set>
#include <cmath>
typedef long long ll;
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
vector<ll> factors(ll n, vector<int> const & primes) {
    vector<ll> result;
    for (int p : primes) {
        if (n < p *(ll) p) break;
        while (n % p == 0) {
            result.push_back(p);
            n /= p;
        }
    }
    if (n != 1) result.push_back(n);
    return result;
}

int main() {
    ll n; scanf("%lld", &n);
    auto primes = sieve_of_eratosthenes(sqrt(n));
    vector<ll> ps = factors(n, primes);
    set<ll> qs;
    for (ll p : ps) {
        if (qs.count(p)) {
            qs.erase(p);
        } else {
            qs.insert(p);
        }
    }
    ll r = 1;
    for (ll q : qs) r *= q;
    printf("%lld\n", r);
    return 0;
}
```
