---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/177/
  - /blog/2016/01/16/yuki-177/
date: 2016-01-16T02:18:38+09:00
tags: [ "competitive", "writeup", "yukicoder", "graph", "flow", "maximum-flow", "edmonds-karp" ]
---

# Yukicoder No.177 制作進行の宮森あおいです！

フローに強くなりたいので解いている。
事前にフローと知らされていればさすがにやるだけな問題。

## [No.177 制作進行の宮森あおいです！](http://yukicoder.me/problems/177)

### 解説

素直な最大流。下みたいなやつ。

![](/blog/2016/01/16/yuki-177/a.png)

<!--
    ```
        digraph G {
            graph [ rankdir = LR, bgcolor="#00000000" ]
            node [ shape = circle, style=filled, fillcolor="#ffffffff" ]
            s [ rank = source ]
            t [ rank = sink ]
            X_1 -&gt; X_2 [ style = invis ]
            X_2 -&gt; X_3 [ style = invis ]
            X_3 -&gt; X_4 [ style = invis ]
            { rank = same; X_1; X_2; X_3; X_4; }
            Y_1 -&gt; Y_2 [ style = invis ]
            Y_2 -&gt; Y_3 [ style = invis ]
            { rank = same; Y_1; Y_2; Y_3; }
            #
            s -&gt; X_1 [ label = J_1 ]
            s -&gt; X_2 [ label = J_2 ]
            s -&gt; X_3 [ label = J_3 ]
            s -&gt; X_4 [ label = J_4 ]
            X_1 -&gt; Y_1 [ label = "\infty" ]
            X_1 -&gt; Y_3 [ label = "\infty" ]
            X_2 -&gt; Y_1 [ label = "\infty" ]
            X_2 -&gt; Y_2 [ label = "\infty" ]
            X_3 -&gt; Y_3 [ label = "\infty" ]
            X_4 -&gt; Y_1 [ label = "\infty" ]
            X_4 -&gt; Y_2 [ label = "\infty" ]
            Y_1 -&gt; t [ label = C_1 ]
            Y_2 -&gt; t [ label = C_2 ]
            Y_3 -&gt; t [ label = C_3 ]
        }
    ```
    -->


### 実装

`maximum_flow()`は[Yukicoder No.119](/blog/2016/01/15/yuki-119/)のものと同一。

``` c++
#include <iostream>
#include <vector>
#include <set>
#include <queue>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
using namespace std;
ll maximum_flow(int s, int t, vector<vector<ll> > const & g /* capacity, adjacency matrix */) { // edmonds karp, O(E^2V)
    int n = g.size();
    vector<vector<ll> > flow(n, vector<ll>(n));
    auto residue = [&](int i, int j) { return g[i][j] - flow[i][j]; };
    ll result = 0;
    while (true) {
        vector<int> prev(n, -1);
        vector<ll> f(n);
        // find the shortest augmenting path
        queue<int> q; // bfs
        q.push(s);
        while (not q.empty()) {
            int i = q.front(); q.pop();
            repeat (j,n) if (prev[j] == -1 and j != s and residue(i,j) > 0) {
                prev[j] = i;
                f[j] = residue(i,j);
                if (i != s) f[j] = min(f[j], f[i]);
                q.push(j);
            }
        }
        if (prev[t] == -1) break; // not found
        // backtrack
        for (int i = t; prev[i] != -1; i = prev[i]) {
            int j = prev[i];
            flow[j][i] += f[t];
            flow[i][j] -= f[t];
        }
        result += f[t];
    }
    return result;
}
int main() {
    // input
    int w, n; cin >> w >> n;
    vector<int> j(n); repeat (i,n) cin >> j[i];
    int m; cin >> m;
    vector<int> c(m); repeat (i,m) cin >> c[i];
    vector<set<int> > x(m);
    repeat (i,m) {
        int q; cin >> q;
        repeat (j,q) {
            int y; cin >> y;
            x[i].insert(y-1);
        }
    }
    // make a capacity graph
    const ll INF = 1000000007;
    vector<vector<ll> > g(n+m+2, vector<ll>(n+m+2));
    int s = n+m, t = n+m+1;
    repeat (i,n) g[s][i] = j[i];
    repeat (j,m) g[n+j][t] = c[j];
    repeat (j,m) repeat (i,n) if (not x[j].count(i)) g[i][n+j] = INF;
    // output
    cout << (maximum_flow(s,t,g) >= w ? "SHIROBAKO" : "BANSAKUTSUKITA") << endl;
    return 0;
}
```
