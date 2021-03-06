---
layout: post
redirect_from:
  - /writeup/algo/codeforces/697-d/
  - /blog/2016/07/15/cf-697-d/
date: "2016-07-15T04:00:27+09:00"
tags: [ "competitive", "writeup", "codeforces", "tree", "expected-value" ]
"target_url": [ "http://codeforces.com/contest/697/problem/D" ]
---

# Codeforces Round #362 (Div. 2) D. Puzzles

## problem

根付き木がある。
この木をDFSすることを考える。
子に移動するときは一様なランダムな順序で移動するとする。
各頂点に関して、通りがけ順で何番目に訪問されるか、その期待値を答えよ。

## solution

DFS twice. $O(N)$.

Fix the parent vertex $p$ and the children $a, b$.
The probability which $a$ is visited after $b$ and the one which $b$ is visited after $a$ is the same, the both are $0.5$.
So, if $p$ has only children $a, b$, $e_a = e_p + \frac{s_b}{2} + 1$ where $e_v$ is the expected value of vertex $v$ and $s_v$ is the size of the subtree $v$.
Generally, $e_u = e_p + \Sigma\_{v, v \text{is a child of} p, v \ne u} \frac{s_v}{2} + 1$.

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <functional>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
using namespace std;
int main() {
    // input
    int n; scanf("%d", &n);
    vector<vector<int> > children(n);
    repeat (i,n-1) {
        int p; scanf("%d", &p);
        children[p-1].push_back(i+1);
    }
    // compute
    vector<int> children_count(n); {
        function<int (int)> dfs = [&](int i) {
            for (int j : children[i]) {
                children_count[i] += dfs(j);
            }
            return children_count[i] + 1;
        };
        dfs(0);
    }
    vector<double> ans(n); {
        function<void (int, double)> dfs = [&](int i, double current_time) {
            current_time += 1;
            ans[i] += current_time;
            for (int j : children[i]) {
                dfs(j, ans[i] + (children_count[i] - (children_count[j]+1)) / 2.0);
            }
        };
        dfs(0, 0.0);
    }
    // output
    repeat (i,n) {
        printf("%.8lf%s", ans[i], i == n-1 ? "\n" : " ");
    }
    return 0;
}
```
