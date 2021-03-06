---
layout: post
redirect_from:
  - /writeup/algo/aoj/2446/
  - /blog/2017/06/28/aoj-2446/
date: "2017-06-28T09:13:00+09:00"
tags: [ "competitive", "writeup", "aoj", "jag-summer", "gcd", "fast-mobius-transformation", "inclusion-exclusion-principle" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2446" ]
---

# AOJ 2446: Enumeration

## solution

選ばれた整数の集合について総当たり。包除原理。高速Mobius変換。$O(n2^n)$。

$2^n$個ある部分集合について、そのlcmを取った値の倍数がいくつ含まれるかを関数$f : 2^n \to \mathbb{N}$とする。
ただし空集合は$0$に写す。
overflowにも注意。
$2^n$個ある部分集合について、その要素の少なくともひとつで割り切れるもののの個数を求める関数$g : 2^n \to \mathbb{N}$とする。
このとき確率$p(X)$を使って答え$\mathrm{ans} = \sum\_{X \subseteq n} p(X)g(X)$。
$g(X) = \sum\_{Y \subseteq X} (-1)^{\|Y\|-1} f(Y)$と書ける。
例えば、(記法の乱用として添字$i$でなくて対応する整数$a\_i$を使って)$g(\\{ 3, 5 \\}) = f(\\{ 3 \\}) + f(\\{ 5 \\}) - f(\\{ 3, 5 \\}) = 5 + 3 - 1  = 7$。
これは高速Mobius変換で$O(n2^n)$で処理できる。


## implementation

``` c++
#include <algorithm>
#include <cstdio>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_from(i, m, n) for (int i = (m); (i) < int(n); ++(i))
using ll = long long;
using namespace std;
template <typename T> T gcd(T a, T b) { while (a) { b %= a; swap(a, b); } return b; }

/**
 * @brief fast mobius transformation
 * @note for f : 2^n \to R; \mu f(Y) = \sum\_{X \subseteq Y} (-1)^{\|Y \setminues X\|} f(X)
 * @note O(n2^n)
 * @see http://pekempey.hatenablog.com/entry/2016/10/30/205852
 * @param T is a commutative group
 */
template <typename T>
vector<T> fast_mobius_transform(vector<T> f) {
    int pow_n = f.size();
    for (int i = 1; i < pow_n; i <<= 1) {
        repeat (s, pow_n) {
            if (s & i) {
                f[s] -= f[s ^ i];
            }
        }
    }
    return f;
}

int main() {
    // input
    int n; ll m; scanf("%d%lld", &n, &m);
    vector<ll> a(n); repeat (i, n) scanf("%lld", &a[i]);
    vector<int> p(n); repeat (i, n) scanf("%d", &p[i]);
    // solve
    vector<ll> cnt(1 << n);
    repeat_from (s, 1, 1 << n) {
        ll acc = 1;
        repeat (i, n) if (s & (1 << i)) {
            ll a_i = a[i] / gcd(acc, a[i]);
            double estimated = acc *(double) a_i;
            if (m < estimated) {
                acc = m+1;
                break;
            }
            acc *= a_i;
        }
        cnt[s] = m / acc;
    }
    vector<ll> mu_cnt = fast_mobius_transform(cnt);
    long double result = 0;
    repeat (s, 1 << n) {
        long double acc = 1;
        repeat (i, n) acc *= (s & (1 << i) ? p[i] : 100 - p[i]) /(long double) 100.0;
        result += abs(mu_cnt[s]) * acc;
    }
    // output
    printf("%.10lf\n", (double)result);
    return 0;
}
```
