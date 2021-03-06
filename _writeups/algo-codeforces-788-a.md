---
layout: post
redirect_from:
  - /writeup/algo/codeforces/788-a/
  - /blog/2017/03/30/cf-788-a/
date: "2017-03-30T03:12:35+09:00"
tags: [ "competitive", "writeup", "codeforces", "dp" ]
"target_url": [ "http://codeforces.com/contest/788/problem/A" ]
---

# Codeforces Round #407 (Div. 1): A. Functions again

## problem

数列$a$が与えられる。
その差分の絶対値の列$\Delta_i = \|a_i - a\_{i+1}\|$を考える。
区間$[l,r]$中のそれを偶奇で符号を変えつつ足し合わせた値$f(l,r) = \sum \Delta\_{l+i} \cdot (-1)^i$の最大値を答えよ。

## solution

DP。$O(N)$。

愚直$[l,r)$ごとに求めると$O(N^3)$、$l$を固定して$r$をまとめてやると$O(N^2)$である。
ある点まで見ているときにその左側部分の和は大きければ大きいほどいいので、これで$l$をまとめてやると$O(N)$。

## implementation

``` c++
#include <cstdio>
#include <cstdlib>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
using ll = long long;
using namespace std;
template <class T> inline void setmax(T & a, T const & b) { a = max(a, b); }
constexpr ll inf = ll(1e18)+9;
int main() {
    int n; scanf("%d", &n);
    vector<int> a(n); repeat (i,n) scanf("%d", &a[i]);
    vector<int> delta(n-1);
    repeat (i,n-1) delta[i] = abs(a[i] - a[i+1]);
    ll result = - inf;
    ll acc[2] = {};
    repeat (i,n-1) {
        repeat (p,2) {
            if (i % 2 == p) setmax(acc[p], 0ll);
            acc[p] += delta[i] * (i % 2 == p ? 1 : -1);
            setmax(result, acc[p]); // when p == 1 and i == 0, it's a negative number.
        }
    }
    printf("%lld\n", result);
    return 0;
}
```
