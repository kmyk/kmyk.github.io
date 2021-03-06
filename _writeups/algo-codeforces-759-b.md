---
layout: post
redirect_from:
  - /writeup/algo/codeforces/759-b/
  - /blog/2018/03/31/cf-759-b/
date: "2018-03-31T01:27:48+09:00"
tags: [ "competitive", "writeup", "codeforces", "dp", "segment-tree", "inline-dp" ]
"target_url": [ "http://codeforces.com/contest/759/problem/B" ]
---

# Codeforces Round #393 (Div. 1) (8VC Venture Cup 2017 - Final Round Div. 1 Edition): B. Travel Card

## problem

よく分かってないため略。
入力はなんだか累積和取った後みたいなものが与えられる。
サンプルは十分強い。

## solution

実家DP。$O(n \log n)$。

## note

-   問題文が難しい 2

## implementation

``` c++
#include <bits/stdc++.h>
#define REP(i, n) for (int i = 0; (i) < int(n); ++ (i))
#define REP_R(i, n) for (int i = int(n) - 1; (i) >= 0; -- (i))
#define ALL(x) begin(x), end(x)
using ll = long long;
using namespace std;

template <class Monoid>
struct segment_tree {
    typedef typename Monoid::underlying_type underlying_type;
    int n;
    vector<underlying_type> a;
    const Monoid mon;
    segment_tree() = default;
    segment_tree(int a_n, underlying_type initial_value = Monoid().unit(), Monoid const & a_mon = Monoid()) : mon(a_mon) {
        n = 1; while (n < a_n) n *= 2;
        a.resize(2 * n - 1, mon.unit());
        fill(a.begin() + (n - 1), a.begin() + ((n - 1) + a_n), initial_value); // set initial values
        REP_R (i, n - 1) a[i] = mon.append(a[2 * i + 1], a[2 * i + 2]); // propagate initial values
    }
    void point_set(int i, underlying_type z) { // 0-based
        a[i + n - 1] = z;
        for (i = (i + n) / 2; i > 0; i /= 2) { // 1-based
            a[i - 1] = mon.append(a[2 * i - 1], a[2 * i]);
        }
    }
    underlying_type range_concat(int l, int r) { // 0-based, [l, r)
        underlying_type lacc = mon.unit(), racc = mon.unit();
        for (l += n, r += n; l < r; l /= 2, r /= 2) { // 1-based loop, 2x faster than recursion
            if (l % 2 == 1) lacc = mon.append(lacc, a[(l ++) - 1]);
            if (r % 2 == 1) racc = mon.append(a[(-- r) - 1], racc);
        }
        return mon.append(lacc, racc);
    }
};
struct min_monoid {
    typedef int underlying_type;
    int unit() const { return 1e9 + 7; }
    int append(int a, int b) const { return min(a, b); }
};

int main() {
    // input
    int n; scanf("%d", &n);
    vector<ll> t(n); REP (i, n) scanf("%lld", &t[i]);

    // solve
    vector<ll> acc(n + 1); copy(ALL(t), acc.begin() + 1);
    segment_tree<min_monoid> dp(n + 1);
    dp.point_set(0, 0);
    int j = 0;
    int k = 0;
    REP (i, n) {
        while (acc[i + 1] - acc[j + 1] >=   90) ++ j;
        while (acc[i + 1] - acc[k + 1] >= 1440) ++ k;
        int a = dp.range_concat(i, i + 1) + 20;
        int b = dp.range_concat(j, i + 1) + 50;
        int c = dp.range_concat(k, i + 1) + 120;
        dp.point_set(i + 1, min({ a, b, c }));

        // output
        int result = dp.range_concat(i + 1, i + 2) - dp.range_concat(i, i + 1);
        printf("%d\n", result);
    }
    return 0;
}
```
