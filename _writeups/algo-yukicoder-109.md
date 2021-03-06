---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/109/
  - /blog/2016/10/18/yuki-109/
date: "2016-10-18T20:22:38+09:00"
tags: [ "competitive", "writeup", "yukicoder", "factorial", "inverse", "wilsons-theorem", "fermats-little-theorem" ]
"target_url": [ "http://yukicoder.me/problems/no/109" ]
---

# Yukicoder No.109 N! mod M

Wilsonの定理は知らなかった。
しかし変な制約があるのでこれを中心に考えれば方針は立つ。
サンプルも親切。

## solution

$M$が素数なら$O(\min \\{ N, (M-N) \log M \\})$、合成数なら$O(\sqrt{M})$。$M - N \le 10^5$という制約があるので通る。

$M$が素数の場合。
[Wilsonの定理](https://en.wikipedia.org/wiki/Wilson%27s_theorem):

>   $n \ge 2$ is a prime iff $(n-1)! \equiv -1 \pmod n$

を使う。
ここにFermatの小定理による逆元$a^{-1} \equiv a^{p-2} \pmod p$を掛け合わせていけば、逆から計算できて$O((M-N) \log M)$になる。

$M$が合成数の場合。
$N$がある程度大きいと答えは$0$になる。
特に、$M$の素因数の全てが十分な回数出現し$M \mid n!$であるような$n$以降で$0$である。
この境界は$\sqrt{M}$を越えないので$O(\sqrt{M})$である。

## implementation

``` c++
#include <iostream>
#include <vector>
#include <map>
#include <cmath>
#include <cassert>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
typedef long long ll;
using namespace std;
template <class T> void setmax(T & a, T const & b) { if (a < b) a = b; }
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
map<ll,int> factors(ll n, vector<int> const & primes) {
    map<ll,int> result;
    for (int p : primes) {
        if (n < p *(ll) p) break;
        while (n % p == 0) {
            result[p] += 1;
            n /= p;
        }
    }
    if (n != 1) result[n] += 1;
    return result;
}
ll powi(ll x, ll y) { // O(log y)
    assert (y >= 0);
    ll z = 1;
    for (ll i = 1; i <= y; i <<= 1) {
        if (y & i) z *= x;
        x *= x;
    }
    return z;
}
ll powi(ll x, ll y, ll p) { // O(log y)
    assert (y >= 0);
    x = (x % p + p) % p;
    ll z = 1;
    for (ll i = 1; i <= y; i <<= 1) {
        if (y & i) z = z * x % p;
        x = x * x % p;
    }
    return z;
}
ll inv(ll x, ll p) { // p must be a prime, O(log p)
    assert ((x % p + p) % p != 0);
    return powi(x, p-2, p);
}

int main() {
    const vector<int> primes = sieve_of_eratosthenes(sqrt(1e9) + 3);
    int t; cin >> t;
    while (t --) {
        ll n, m; cin >> n >> m;
        assert (0 <= n and n <= 1e9);
        assert (1 <= m and m <= 1e9);
        assert (m <= n + 1e5);
        map<ll,int> ps = factors(m, primes);
        ll ans;
        if (ps.empty()) { // m is 1
            ans = 0;
        } else if (ps.size() == 1 and ps.begin()->second == 1) { // m is a prime
            if (m <= n) {
                ans = 0;
            } else {
                ans = m - 1;
                repeat_from (i,n+1,m) {
                    ans = ans * inv(i, m) % m;
                }
            }
        } else { // m is a composite
            ll limit = 0;
            for (auto it : ps) {
                ll p; int cnt; tie(p, cnt) = it;
                int k = 0; while (k * (k+1) / 2 < cnt) ++ k;
                setmax(limit, p * k);
            }
            if (limit <= n) {
                ans = 0;
            } else {
                ans = 1;
                repeat_from (i,1,n+1) {
                    ans = ans * i % m;
                }
                assert (ans != 0);
            }
        }
        cout << ans << endl;
    }
    return 0;
}
```
