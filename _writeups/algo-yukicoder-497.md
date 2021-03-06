---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/497/
  - /blog/2017/03/27/yuki-497/
date: "2017-03-27T17:02:54+09:00"
tags: [ "competitive", "writeup", "yukicoder", "graph" ]
"target_url": [ "http://yukicoder.me/problems/no/497" ]
---

# Yukicoder No.497 入れ子の箱

<!-- {% raw %} -->

>   任意に向きを変えたり横倒しにしたりした後の

というのを見て、例えば$40^\circ$回転なども許すような面倒な$3$次元幾何 (参考: <https://beta.atcoder.jp/contests/arc029/tasks/arc029_2>) だと誤読し撤退してしまった。

なお回転なしの場合でも類問がある: <https://beta.atcoder.jp/contests/abc038/tasks/abc038_d>。

## solution

包含関係で有効グラフを作って最長路を取る。$O(N^2)$。

包含関係は思考停止で`next_permutation`すればよい。
DAGになっているのでDPもできるが、雑に探索っぽくするのでも十分。

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <algorithm>
#include <functional>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using namespace std;
int main() {
    int n; scanf("%d", &n);
    vector<int> x(n), y(n), z(n); repeat (i,n) scanf("%d%d%d", &x[i], &y[i], &z[i]);
    vector<vector<int> > g(n);
    vector<int> indegree(n);
    repeat (i,n) {
        array<int, 3> a = {{ x[i], y[i], z[i] }};
        whole(sort, a);
        repeat (j,n) {
            bool found = false;
            do {
                if (a[0] < x[j] and a[1] < y[j] and a[2] < z[j]) {
                    found = true;
                }
            } while (whole(next_permutation, a));
            if (found) {
                g[j].push_back(i);
                indegree[i] += 1;
            }
        }
    }
    vector<int> depth(n, -1);
    function<void (int, int)> dfs = [&](int i, int cur_depth) {
        if (depth[i] < cur_depth) {
            depth[i] = cur_depth;
            for (int j : g[i]) {
                dfs(j, cur_depth + 1);
            }
        }
    };
    repeat (i,n) if (indegree[i] == 0) {
        dfs(i, 0);
    }
    int max_depth = *whole(max_element, depth);
    printf("%d\n", max_depth + 1);
    return 0;
}
```

<!-- {% endraw %} -->
