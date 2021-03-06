---
layout: post
redirect_from:
  - /writeup/algo/csacademy/38-d/
  - /writeup/algo/cs-academy/38-d/
  - /blog/2017/07/20/csa-38-d/
date: "2017-07-20T03:12:02+09:00"
tags: [ "competitive", "writeup", "csacademy", "tree", "random" ]
---

# CS Academy Round #38: D. Tree Antichain (Easy)

antichainって$x \perp y \iff \lnot \exists z. z \le x \land z \le y$として$\forall x, y \in A. x \ne y \to x \perp y$だと思ってたけど、$\forall x, y \in A. x \ne y \to \lnot (x \le y) \land \lnot (y \le x)$もそう言うらしい。それはそう。どうせ列に対してでなくて集合に対する性質だけど。

## problem

木が与えられる。その頂点の順列で、その列で隣り合うものは木上で隣り合わないようなものをひとつ答えよ。

## solution

星型 (次数$\|E\|$の頂点が存在する)の木なら不可能でそれ以外は可能。
列の構成は乱択。次数の大きいものに比重を置きながら適当に取っていく。ほとんど$O(N)$だと思う。

何も考えずに乱択をするとさすがにだめで、反例は次数$\|E\| - 1$の頂点があるような場合。

## implementation

``` c++
#include <algorithm>
#include <cstdio>
#include <numeric>
#include <random>
#include <set>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f, x, ...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using namespace std;

default_random_engine gen((random_device())());

vector<int> solve(int n, vector<vector<int> > const & g) {
    repeat (i, n) {
        if (g[i].size() == n - 1) {
            return vector<int>(); // star
        }
    }
    vector<set<int> > g_set(n);
    repeat (i, n) {
        for (int j : g[i]) {
            g_set[i].insert(j);
        }
    }
    while (true) {
        vector<int> rank(n);
        repeat (i, n) {
            rank[i] = g[i].size() + uniform_int_distribution<int>(-2, 4)(gen);
        }
        vector<int> xs(n);
        whole(iota, xs, 0);
        whole(sort, xs, [&](int i, int j) { return rank[i] > rank[j]; });
        vector<int> ys;
        swap(xs[0], xs.back());
        ys.push_back(xs.back());
        xs.pop_back();
        while (not xs.empty()) {
            int i = 0;
            for (; i < xs.size(); ++ i) {
                if (not g_set[ys.back()].count(xs[i])) {
                    swap(xs[i], xs.back());
                    ys.push_back(xs.back());
                    xs.pop_back();
                    i = -1;
                    break;
                }
            }
            if (i == xs.size()) {
                ys.clear();
                break;
            }
        }
        if (not ys.empty()) {
            return ys;
        }
    }
}

int main() {
    int testcases; scanf("%d", &testcases);
    while (testcases --) {
        int n; scanf("%d", &n);
        vector<vector<int> > g(n);
        repeat (i, n - 1) {
            int x, y; scanf("%d%d", &x, &y); -- x; -- y;
            g[x].push_back(y);
            g[y].push_back(x);
        }
        vector<int> result = solve(n, g);
        if (result.empty()) {
            printf("-1\n");
        } else {
            repeat (i, n) {
                printf("%d%c", result[i] + 1, i + 1 == n ? '\n' : ' ');
            }
        }
    }
    return 0;
}
```
