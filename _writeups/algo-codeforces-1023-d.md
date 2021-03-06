---
redirect_from:
  - /writeup/algo/codeforces/1023-d/
layout: post
date: 2018-08-18T02:06:40+09:00
tags: [ "competitive", "writeup", "codeforces", "segment-tree", "corner-cases" ]
"target_url": [ "http://codeforces.com/contest/1023/problem/D" ]
---

# Codeforces Round #504 (rated, Div. 1 + Div. 2, based on VK Cup 2018 Final): D. Array Restoration

## solution

列$a$中に$j = a_i$が出現すれば区間$[l_j, r_j)$は$i$を含まなければならない。
そのようにしてクエリを復元し実行してみて上手く塗れるか確認すればよい。
$O(n + q \log n)$。

クエリの実行は遅延評価segment木とかで適当にやる。
コーナーケースとして$r_j - l_j \ge 1$でなければならないのには注意。
例えば次のような入力の答えは `NO` である。

```
1 2
1
```

## note

コーナーケース踏んで2WAしましたひどすぎる

## implementation

``` c++
#include <bits/stdc++.h>
#define REP(i, n) for (int i = 0; (i) < (int)(n); ++ (i))
#define REP3(i, m, n) for (int i = (m); (i) < (int)(n); ++ (i))
#define ALL(x) begin(x), end(x)
using namespace std;
template <class T> inline void chmax(T & a, T const & b) { a = max(a, b); }
template <class T> inline void chmin(T & a, T const & b) { a = min(a, b); }

template <class OperatorMonoid>
struct dual_segment_tree {
    typedef typename OperatorMonoid::underlying_type operator_type;
    typedef typename OperatorMonoid::target_type underlying_type;
    int n;
    vector<operator_type> f;
    vector<underlying_type> a;
    const OperatorMonoid op;
    dual_segment_tree() = default;
    dual_segment_tree(int a_n, underlying_type initial_value, OperatorMonoid const & a_op = OperatorMonoid()) : op(a_op) {
        n = 1; while (n < a_n) n *= 2;
        a.resize(n, initial_value);
        f.resize(n - 1, op.unit());
    }
    underlying_type point_get(int i) { // 0-based
        underlying_type acc = a[i];
        for (i = (i + n) / 2; i > 0; i /= 2) { // 1-based
            acc = op.apply(f[i - 1], acc);
        }
        return acc;
    }
    void range_apply(int l, int r, operator_type z) { // 0-based, [l, r)
        assert (0 <= l and l <= r and r <= n);
        range_apply(0, 0, n, l, r, z);
    }
    void range_apply(int i, int il, int ir, int l, int r, operator_type z) {
        if (l <= il and ir <= r) { // 0-based
            if (i < f.size()) {
                f[i] = op.append(z, f[i]);
            } else {
                a[i - n + 1] = op.apply(z, a[i - n + 1]);
            }
        } else if (ir <= l or r <= il) {
            // nop
        } else {
            range_apply(2 * i + 1, il, (il + ir) / 2, 0, n, f[i]);
            range_apply(2 * i + 2, (il + ir) / 2, ir, 0, n, f[i]);
            f[i] = op.unit();
            range_apply(2 * i + 1, il, (il + ir) / 2, l, r, z);
            range_apply(2 * i + 2, (il + ir) / 2, ir, l, r, z);
        }
    }
};
struct const_operator_monoid {
    typedef int underlying_type;
    typedef int target_type;
    int unit() const { return -1; }
    int append(int a, int b) const { return a == -1 ? b : a; }
    int apply(int a, int b) const { return a == -1 ? b : a; }
};

vector<int> solve(int n, int q, vector<int> const & a) {
    vector<int> l(q + 1, INT_MAX);
    vector<int> r(q + 1, INT_MIN);
    l[1] = 0;
    r[1] = n;
    REP (i, n) if (a[i] != 0) {
        chmin(l[a[i]], i);
        chmax(r[a[i]], i + 1);
    }

    dual_segment_tree<const_operator_monoid> segtree(n, -1);
    int dangling = -1;
    REP3 (i, 1, q + 1) {
        if (l[i] == INT_MAX) {
            dangling = i;
        } else {
            dangling = -1;
            segtree.range_apply(l[i], r[i], i);
        }
    }
    if (dangling != -1 and not count(ALL(a), 0)) {
        return vector<int>();  // since each query must paint a non-empty segment
    }

    vector<int> b(n);
    REP (i, n) {
        b[i] = segtree.point_get(i);
        if (a[i] == 0) {
            if (dangling != -1) {
                b[i] = dangling;
                dangling = -1;
            }
        } else if (b[i] != a[i]) {
            return vector<int>();
        }
    }
    return b;
}

int main() {
    // input
    int n, q; scanf("%d%d", &n, &q);
    vector<int> a(n);
    REP (i, n) scanf("%d", &a[i]);

    // solve
    vector<int> b = solve(n, q, a);

    // output
    if (b.empty()) {
        printf("NO\n");
    } else {
        printf("YES\n");
        for (int b_i : b) {
            printf("%d ", b_i);
        }
        printf("\n");
    }
    return 0;
}
```
