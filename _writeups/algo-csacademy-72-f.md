---
layout: post
redirect_from:
  - /writeup/algo/csacademy/72-f/
  - /writeup/algo/cs-academy/72-f/
  - /blog/2018/03/08/csa-72-f/
date: "2018-03-08T12:04:38+09:00"
tags: [ "competitive", "writeup", "csa", "segment-tree", "sparse-table" ]
"target_url": [ "https://csacademy.com/contest/round-72/task/dogs/" ]
---

# CS Academy Round #72. Reversed Number

## solution

犬同士は$O(\log N)$あるいは$O(1)$で比較できる。
`std::sort`して$O(N + M \log M)$。

犬$i, j$の比較にはsegment treeやsparse tableを使う。
$[L\_i, R\_i]$と$[L\_j, R\_j]$が交わらない場合はそれらの区間の最大値で比較する。
$T$のdistinct制約が効いている。
交わりがある場合も、一方にのみ所属する区間の最大値で比較すればよい。

## implementation

``` c++
#include <bits/stdc++.h>
#define REP(i, n) for (int i = 0; (i) < int(n); ++ (i))
#define ALL(x) begin(x), end(x)
using namespace std;

template <class Semilattice>
struct sparse_table {
    typedef typename Semilattice::underlying_type underlying_type;
    vector<vector<underlying_type> > table;
    Semilattice lat;
    sparse_table() = default;
    sparse_table(vector<underlying_type> const & data, Semilattice const & a_lat = Semilattice())
            : lat(a_lat) {
        int n = data.size();
        int log_n = 32 - __builtin_clz(n);
        table.resize(log_n, vector<underlying_type>(n));
        table[0] = data;
        REP (k, log_n - 1) {
            REP (i, n) {
                table[k + 1][i] = i + (1ll << k) < n ?
                    lat.append(table[k][i], table[k][i + (1ll << k)]) :
                    table[k][i];
            }
        }
    }
    underlying_type range_concat(int l, int r) const {
        if (l == r) return lat.unit();  // if there is no unit, remove this line
        assert (0 <= l and l < r and r <= table[0].size());
        int k = 31 - __builtin_clz(r - l);  // log2
        return lat.append(table[k][l], table[k][r - (1ll << k)]);
    }
};
struct max_semilattice {
    typedef int underlying_type;
    int unit() const { return INT_MIN; }
    int append(int a, int b) const { return max(a, b); }
};

int main() {
    // input
    int n; scanf("%d", &n);
    vector<int> t(n);
    REP (i, n) scanf("%d", &t[i]);
    int m; scanf("%d", &m);
    vector<int> l(m), r(m);
    REP (i, m) {
        scanf("%d%d", &l[i], &r[i]);
        -- l[i];
    }

    // solve
    sparse_table<max_semilattice> rmq(t);
    function<bool (int, int)> cmp = [&](int i, int j) {
        int l1 = l[i], r1 = r[i];
        int l2 = l[j], r2 = r[j];
        if (make_pair(l1, r1) == make_pair(l2, r2)) return false;
        if (make_pair(l1, r1)  > make_pair(l2, r2)) return not cmp(j, i);
        if (r1 <= l2 or r2 <= l1) {  // disjoint
            return rmq.range_concat(l1, r1) < rmq.range_concat(l2, r2);
        } else if (r2 <= r1) {  // included
            return false;
        } else {
            return rmq.range_concat(l1, l2) < rmq.range_concat(r1, r2);
        }
    };
    vector<int> order(m);
    iota(ALL(order), 0);
    sort(ALL(order), [&](int i, int j) { return cmp(i, j); });

    // output
    for (int i : order) {
        printf("%d ", i + 1);
    }
    printf("\n");
    return 0;
}
```
