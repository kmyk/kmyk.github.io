---
layout: post
redirect_from:
  - /writeup/algo/codeforces/679-b/
  - /blog/2016/06/09/cf-679-b/
date: 2016-06-09T05:25:20+09:00
tags: [ "competitive", "writeup", "codeforces" ]
---

# Codeforces Round #356 (Div. 1) B. Bear and Tower of Cubes

-   <http://codeforces.com/contest/679/problem/B>
-   [editorial](http://codeforces.com/blog/entry/45310)

嘘っぽい定数倍高速化で通そうとしたが、だめでした。

## problem

各辺の長さが整数の立方体をその体積の合計が$x$を越えないように貪欲に積み上げた結果の体積を$f(x)$とする。
$\max \\{ (f(x), x) \mid x \le m \\}$を答えよ。

## solution

Recursion on $m$. For each $m$, how many times is the maximal block $a$ used, is $m \bmod a^3$ or $(m \bmod a^3) - 1$, using the monotonicity of the function.

Let $a = \max \\{ a \mid a^3 \le m \\}$, the maximal block.
Think whether the block $a$ is used or not.
Assume it is not used, the result is equivalent to one for $m' = a^3 -1$.
But under this assumption, you can also use $p - 1$ blocks of $a$ if $m = p \dot a^3 + q$, and use the same blocks for $m' = a^3 -1$.
So you always use at least $(m \bmod a^3) - 1$ blocks with side $a$, and also use blocks for $m' = a^3-1$ or use blocks for $m' = m \bmod a^3$ and one block of $a$.


## implementation

``` c++
#include <iostream>
#include <unordered_map>
#include <tuple>
#include <cmath>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
using namespace std;
template <class T> bool setmax(T & l, T const & r) { if (not (l < r)) return false; l = r; return true; }

ll cube(ll a) { return a * a * a; }
ll cbrt(ll x) {
    ll y = pow(x, 1/3.) - 3;
    while (cube(y+1) <= x) ++ y;
    return y;
}

unordered_map<ll,pair<ll,ll> > memo;
pair<ll,ll> f(ll m) {
    if (m == 0) return { 0, 0 };
    if (memo.count(m)) return memo[m];
    ll a = cbrt(m);
    auto use = [=](ll k) {
        ll y, x; tie(y, x) = f(min(cube(a)-1, m - k * cube(a)));
        y += k;
        x += k * cube(a);
        return make_pair(y, x);
    };
    pair<ll,ll> ans = { 0, 0 };
    setmax(ans, use(m / cube(a)));
    setmax(ans, use(m / cube(a) - 1));
    return memo[m] = ans;
}

int main() {
    ll m; cin >> m;
    ll y, x; tie(y, x) = f(m);
    cout << y << ' ' << x << endl;
    return 0;
}
```
