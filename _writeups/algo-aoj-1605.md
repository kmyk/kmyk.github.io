---
layout: post
redirect_from:
  - /writeup/algo/aoj/1605/
  - /blog/2017/07/23/aoj-1605/
date: "2017-07-23T04:49:06+09:00"
tags: [ "competitive", "writeup", "aoj", "icpc-domestic", "tree", "minimum-spanning-tree", "binary-search" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1605" ]
---

# AOJ 1605. 橋の建造計画 / Bridge Construction Planning

分からない。

## solution

$A$社の辺に一様に重み$a$を乗せて最小全域木を求め、そのとき使われる$A$社の辺の数を$e(a)$、重みを$w'(a)$とする。
$w'(a)$が同じになるなら$e(a)$はできるだけ小さくなるようにする。つまり$B$の辺を優先的に使う。
二分探索で$k = e(a)$な重み$a \in \mathbb{Z}$を求め、(それがあれば) $w = w'(a) - ka$が答え。$O(\max w\_i \cdot M \log M \log)$。


まず不可能な場合を弾く。
適当な$a$に対し$e(a)$を求め、そもそも木ができなかった場合は$\mathrm{ans} = -1$。
木が構成できても常に$A$社の辺か$B$社の辺が過剰に使われる場合は$\mathrm{ans} = -1$、つまり$e(+ \infty), e(- \infty)$の両方が$\gt k$あるいは両方が$\lt k$なら$\mathrm{ans} = -1$。

そうでない場合は条件を満たす木が存在、つまり$e(a) \le k \le e(b)$となるような$a, b \in \mathbb{Z}$が存在。
$a$の増加に対し$e(a)$は単調に減少するので、特に$e(a) \le k \lt e(a - 1)$な$a \in \mathbb{Z}$が二分探索で求まる。
$e(a) = k$なら答え$\mathrm{ans} = w = w'(a) - ak$として求まる。

ちょうどは一致しなかった場合、つまり$e(a) \lt k \lt e(a - 1)$の場合は問題。
しかし気にせず$w = w'(a) - ka$でよい。$e(a) \lt k \lt e(a - 1)$なので、補正後の重みが拮抗している部分で上手く辺を得らべば$e(a) = k$にできるということであるため (のはず)。
ここで注意として、$w'(a)$が同じなら$e(a)$は小さくなるように木を構築している必要がある。
そのようにした場合は二分探索で見つかる$a$は小さくなり当然答えは大きくなるが、逆にそうしなかった場合はここで$B$の辺を$A$の辺に移し換えることができなくなってしまう。

<hr>

$a$は小さくなり答えが大きくなる副作用が不安であるので正当性を示す。
**示したいけどなんだか示せない気がしてきたので放棄** 二分探索の条件式おかしい気がするし動いてるの偶然なのでは。ACしたしそんなはずはないのだけど

## implementation

``` c++
#include <algorithm>
#include <cassert>
#include <cstdio>
#include <tuple>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f, x, ...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using ll = long long;
using namespace std;
template <class T> inline void setmin(T & a, T const & b) { a = min(a, b); }

struct disjoint_sets {
    vector<int> data;
    disjoint_sets() = default;
    explicit disjoint_sets(size_t n) : data(n, -1) {}
    bool is_root(int i) { return data[i] < 0; }
    int find_root(int i) { return is_root(i) ? i : (data[i] = find_root(data[i])); }
    int set_size(int i) { return - data[find_root(i)]; }
    int union_sets(int i, int j) {
        i = find_root(i); j = find_root(j);
        if (i != j) {
            if (set_size(i) < set_size(j)) swap(i,j);
            data[i] += data[j];
            data[j] = i;
        }
        return i;
    }
    bool is_same(int i, int j) { return find_root(i) == find_root(j); }
};

template <typename UnaryPredicate>
ll binsearch(ll l, ll r, UnaryPredicate p) { // [l, r), p is monotone
    assert (l < r);
    -- l;
    while (r - l > 1) {
        ll m = (l + r) / 2;
        (p(m) ? r : l) = m;
    }
    return r; // = min { x in [l, r) | p(x) }, or r
}

template <typename T> struct weighted_edge { int u, v; T cost; bool is_a; };
template <typename T> bool operator < (weighted_edge<T> const & a, weighted_edge<T> const & b) { return make_pair(a.cost, a.is_a) < make_pair(b.cost, b.is_a); } // weak ordering
tuple<int, int, int> minimum_spanning_tree(int n, vector<vector<pair<int, int> > > const & g, vector<vector<pair<int, int> > > const & h, int a) {
    vector<weighted_edge<int> > edges;
    repeat (p, 2) {
        repeat (i, n) {
            for (auto edge : (p ? g : h)[i]) {
                int j, cost; tie(j, cost) = edge;
                if (i < j) edges.push_back((weighted_edge<int>) { i, j, cost + (p ? a : 0), bool(p) });
            }
        }
    }
    whole(sort, edges);
    int a_count = 0;
    int b_count = 0;
    int total_cost = 0;
    disjoint_sets sets(n);
    for (auto e : edges) { // Kruskal's method, O(E \log E)
        if (not sets.is_same(e.u, e.v)) {
            sets.union_sets(e.u, e.v);
            (e.is_a ? a_count : b_count) += 1;
            total_cost += e.cost;
        }
    }
    return make_tuple(a_count, b_count, total_cost);
}
int count_a_edges(int n, vector<vector<pair<int, int> > > const & g, vector<vector<pair<int, int> > > const & h, int a) {
    int a_count, b_count;
    tie(a_count, b_count, ignore) = minimum_spanning_tree(n, g, h, a);
    if (a_count + b_count != n - 1) return -1;
    return a_count;
}

constexpr int W_MAX = 100;
int main() {
    while (true) {
        int n, m, k; scanf("%d%d%d", &n, &m, &k);
        if (n == 0 and m == 0 and k == 0) break;
        vector<vector<pair<int, int> > > g(n); // of A
        vector<vector<pair<int, int> > > h(n); // of B
        repeat (i, m) {
            int u, v, w; char l; scanf("%d%d%d %c", &u, &v, &w, &l); -- u; -- v;
            assert (l == 'A' or l == 'B');
            (l == 'A' ? g : h)[u].emplace_back(v, w);
            (l == 'A' ? g : h)[v].emplace_back(u, w);
        }
        constexpr int inf = 10 * W_MAX;
        int pinf_a = count_a_edges(n, g, h, + inf);
        int ninf_a = count_a_edges(n, g, h, - inf);
        int result;
        if (pinf_a == -1 or ninf_a == -1 or (pinf_a - k) * (ninf_a - k) > 0) {
            result = -1;
        } else {
            int a = binsearch(- 2 * W_MAX, 3 * W_MAX, [&](int a) {
                int a_count = count_a_edges(n, g, h, a);
                return a_count <= k;
            });
            int a_count, total_cost;
            tie(a_count, ignore, total_cost) = minimum_spanning_tree(n, g, h, a);
            assert (a_count <= k);
            result = total_cost - a * k;
        }
        printf("%d\n", result);
    }
    return 0;
}
```

