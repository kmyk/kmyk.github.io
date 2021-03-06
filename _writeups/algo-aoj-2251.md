---
layout: post
redirect_from:
  - /writeup/algo/aoj/2251/
  - /blog/2016/06/30/aoj-2251/
date: 2016-06-30T22:59:35+09:00
tags: [ "competitive", "writeup", "aoj", "dag", "flow", "direct-acyclic-graph", "ford-fulkerson", "minimum-path-cover", "warshall-floyd" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2251" ]
---

# AOJ 2251: Merry Christmas

解説を見た。
DAGになってて二部マッチングを使うという所までは見えていたが、入次数が$0$な点を集めてきて更新するのを繰り返すのを書いてWAを貰っていた。実装のミスだったかもしれないが確認はしていない。

## problem

$N$頂点の単純グラフが与えられる。
辺には頂点間の距離が重みとして乗っている。

時刻$t_i$ちょうどにサンタが頂点$v_i$に居なければならない、という配達の制約が$L$個与えられる。
サンタは好きな時刻に好きな頂点に配置でき、好きな時刻に頂点から頂点へその間の辺の重み分の時間を掛けて移動できる。
制約を全て満たすために、新規に配置しなければならないサンタの数を答えよ。

## solution

DAGに落としてpath cover。$O(N + MQ)$。

直接与えられる距離のグラフにwarshall floydを用い、ある配達の制約同士の間の遷移可能性に落とす。
これは時間を遡ることがないため半順序になっていて、DAGである。
必要なサンタの人数はこの上の最小path被覆の大きさと言い換えられるので、これを求める。
これは二部グラフの最大マッチングとして求められる。

## implementation

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <set>
#include <map>
#include <functional>
#include <cassert>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) y) { return (f)(begin(y), end(y), ## __VA_ARGS__); })(x)
template <class T> bool setmin(T & l, T const & r) { if (not (r < l)) return false; l = r; return true; }
using namespace std;

struct edge_t { int to, cap, rev; };
int maximum_flow_destructive(int s, int t, vector<vector<edge_t> > & g) { // ford fulkerson, O(FE)
    int n = g.size();
    vector<bool> used(n);
    function<int (int, int)> dfs = [&](int i, int f) {
        if (i == t) return f;
        used[i] = true;
        for (edge_t & e : g[i]) {
            if (used[e.to] or e.cap <= 0) continue;
            int nf = dfs(e.to, min(f, e.cap));
            if (nf > 0) {
                e.cap -= nf;
                g[e.to][e.rev].cap += nf;
                return nf;
            }
        }
        return 0;
    };
    int result = 0;
    while (true) {
        used.clear(); used.resize(n);
        int f = dfs(s, numeric_limits<int>::max());
        if (f == 0) break;
        result += f;
    }
    return result;
}

vector<pair<int,int> > perfect_bipartite_matching(set<int> const & a, set<int> const & b, vector<vector<int> > const & g /* adjacency list */) { // O(V + FE)
    assert (a.size() + b.size() <= g.size());
    int n = g.size();
    int src = n;
    int dst = n + 1;
    vector<vector<edge_t> > h(n + 2);
    auto add_edge = [&](int from, int to, int cap) {
        h[from].push_back((edge_t) {   to, cap, int(h[  to].size()    ) });
        h[  to].push_back((edge_t) { from,   0, int(h[from].size() - 1) });
    };
    repeat (i,n) {
        if (a.count(i)) {
            add_edge(src, i, 1);
            for (int j : g[i]) if (b.count(j)) {
                add_edge(i, j, 1); // collect edges e : a -> b, from g
            }
        }
        if (b.count(i)) {
            add_edge(i, dst, 1);
        }
    }
    maximum_flow_destructive(src, dst, h);
    vector<pair<int,int> > ans;
    for (int from : a) {
        for (edge_t e : h[from]) if (b.count(e.to) and e.cap == 0) {
            ans.emplace_back(from, e.to);
        }
    }
    return ans;
}

const int inf = 1e9+7;
int main() {
    while (true) {
        // input
        int n, m, l; cin >> n >> m >> l;
        if (n == 0 and m == 0 and l == 0) break;
        vector<map<int,int> > g(n);
        repeat (i,m) {
            int u, v, d; cin >> u >> v >> d;
            g[u][v] = d;
            g[v][u] = d;
        }
        vector<int> p(l), t(l);
        repeat (i,l) cin >> p[i] >> t[i];
        // warshall floyd
        vector<vector<int> > dist(n, vector<int>(n, inf));
        repeat (i,n) dist[i][i] = 0;
        repeat (i,n) for (auto it : g[i]) dist[i][it.first] = it.second;
        repeat (k,n) repeat (i,n) repeat (j,n) setmin(dist[i][j], dist[i][k] + dist[k][j]);
        // make a digraph of presents, DAG
        vector<vector<int> > h(2*l);
        repeat (i,l) repeat (j,l) if (i != j) if (dist[p[i]][p[j]] <= t[j] - t[i]) h[i].push_back(j + l);
        // let it flow
        set<int> a, b;
        repeat (i,l) a.insert(i);
        repeat (j,l) b.insert(j + l);
        auto move = perfect_bipartite_matching(a, b, h);
        // output
        cout << l - move.size() << endl;
    }
    return 0;
}
```
