---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/526/
  - /blog/2017/07/06/yuki-526/
date: "2017-07-06T14:42:02+09:00"
tags: [ "competitive", "writeup", "yukicoder", "fibonacci" ]
"target_url": [ "https://yukicoder.me/problems/no/526" ]
---

# Yukicoder No.526 フィボナッチ数列の第N項をMで割った余りを求める

行列累乗でも再帰でも$O(\log N)$だが、再帰を展開すると最高速かなと思ってやってみた。
入力が小さすぎて少なくともこの問題では比較にならず、実行時間最短はバイナリの小ささとかに依存しそう。
また$M \lt 2^{31}$とちょっと大きいので面倒が発生した。

式はWikipediaに載ってる: <https://en.wikipedia.org/wiki/Fibonacci_number#Matrix_form>

## implementation

``` c++
#include <cassert>
#include <cstdio>
using ll = long long;

int fib(ll n, int mod) {
    assert (n >= 0);
    if (n <= 1) return n;
    int a = 0;
    int b = 1;
    ll i = 1ll << (63 - __builtin_clzll(n) - 1);
    for (; i; i >>= 1) {
        int na = (a *(ll) a + b *(ll) b) % mod;
        ll nb = 2ll * a + b;
        if (nb >= mod) nb -= mod;
        nb = nb * b % mod;
        a = na;
        b = nb;
        if (n & i) {
            ll c = a +(ll) b; if (c >= mod) c -= mod;
            a = b;
            b = c;
        }
    }
    return b;
}

int main() {
    int n, m; scanf("%d%d", &n, &m);
    printf("%d\n", fib(n - 1, m));
    return 0;
}
```
