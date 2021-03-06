---
redirect_from:
  - /writeup/algo/yukicoder/759/
layout: post
date: 2018-12-07T01:32:54+09:00
tags: [ "competitive", "writeup", "yukicoder", "segment-tree", "sort", "binary-indexed-tree" ]
"target_url": [ "https://yukicoder.me/problems/no/759" ]
---

# Yukicoder No.759 悪くない忘年会にしような！

## 解法

### 概要

ソートしてsegment tree。$$O(N \log N)$$。

### 詳細

とりあえず初手ソートして $$3$$ 次元の制約を $$2$$ 次元に落とす。
すると平面上の領域 $$[b_i, \infty) \times [c_i, \infty)$$ に点が存在するかを判定する形の問題になる。
これは領域中の点集合の特性関数 $$f : X \times Y \to 2$$ を考えることであるが、この形の関数は $$f : X \to Y \cup \{ - \infty \}$$ の形に変形するとよいという一般則があり、segment treeによる区間最大値が思い出される。
あとはこれをやれば解ける。

## メモ

-   binary-indexed treeで十分

## 実装

``` c++
#include <bits/stdc++.h>
#define REP(i, n) for (int i = 0; (i) < (int)(n); ++ (i))
#define REP3(i, m, n) for (int i = (m); (i) < (int)(n); ++ (i))
#define REP_R(i, n) for (int i = int(n) - 1; (i) >= 0; -- (i))
#define ALL(x) begin(x), end(x)
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
        assert (0 <= i and i <= n);
        a[i + n - 1] = z;
        for (i = (i + n) / 2; i > 0; i /= 2) { // 1-based
            a[i - 1] = mon.append(a[2 * i - 1], a[2 * i]);
        }
    }
    underlying_type range_concat(int l, int r) { // 0-based, [l, r)
        assert (0 <= l and l <= r and r <= n);
        underlying_type lacc = mon.unit(), racc = mon.unit();
        for (l += n, r += n; l < r; l /= 2, r /= 2) { // 1-based loop, 2x faster than recursion
            if (l % 2 == 1) lacc = mon.append(lacc, a[(l ++) - 1]);
            if (r % 2 == 1) racc = mon.append(a[(-- r) - 1], racc);
        }
        return mon.append(lacc, racc);
    }
};

struct max_monoid {
    typedef int underlying_type;
    int unit() const { return INT_MIN; }
    int append(int a, int b) const { return max(a, b); }
};

vector<bool> solve(int n, vector<int> const & a, vector<int> const & b, vector<int> const & c) {
    vector<int> order(n);
    iota(ALL(order), 0);
    sort(ALL(order), [&](int i, int j) { return a[i] > a[j]; });
    vector<bool> answer(n, true);
    int max_b = *max_element(ALL(b));
    segment_tree<max_monoid> segtree(max_b + 1);

    for (int l = 0; l < n; ) {
        int r = l + 1;
        while (r < n and a[order[r]] == a[order[l]]) ++ r;
        REP3 (m, l, r) {
            int i = order[m];
            if (segtree.range_concat(b[i], max_b + 1) >= c[i]) answer[i] = false;
        }
        REP3 (m, l, r) {
            int i = order[m];
            segtree.point_set(b[i], max(c[i], segtree.range_concat(b[i], b[i] + 1)));
        }
        REP3 (m, l, r) {
            int i = order[m];
            if (segtree.range_concat(b[i], b[i] + 1) > c[i]) answer[i] = false;
            if (segtree.range_concat(b[i] + 1, max_b + 1) >= c[i]) answer[i] = false;
        }
        l = r;
    }

    return answer;
}

int main() {
    int n; cin >> n;
    vector<int> a(n), b(n), c(n);
    REP (i, n) cin >> a[i] >> b[i] >> c[i];
    auto answer = solve(n, a, b, c);
    REP (i, n) if (answer[i]) {
        cout << i + 1 << endl;
    }
    return 0;
}
```
