---
layout: post
redirect_from:
  - /writeup/algo/aoj/1182/
  - /blog/2017/07/01/aoj-1182/
date: "2017-07-01T22:08:08+09:00"
tags: [ "competitive", "writeup", "aoj", "icpc-domestic", "djikstra", "warshall-floyd", "graph" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1182" ]
---

# AOJ 1182: 鉄道乗り継ぎ / Railway Connection

入力取るだけでも面倒でつらい。折れ線って何だ。

## solution

Warshall-Floyd (とDijkstra)。
$O(cn^2 (n + \max d\_i))$

1.  会社を固定し、その会社の路線だけを使って全頂点対で距離を求める。Warshall-Floydをする
2.  会社を固定し、その会社の路線だけを使って全頂点対で費用を求める。上の結果を用いる
3.  全ての会社を使って、始点からの費用を求める。辺は$1$駅分のみを見るのでなくて、$1$路線を使って行けるところまで行くものとする
    -   Dijkstraでもよいが、Warshall-Floydで十分ぽい

## implementation

``` c++
#include <cstdio>
#include <queue>
#include <tuple>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
using namespace std;
template <class T> inline void setmax(T & a, T const & b) { a = max(a, b); }
template <class T> inline void setmin(T & a, T const & b) { a = min(a, b); }
template <typename X, typename T> auto vectors(X x, T a) { return vector<T>(x, a); }
template <typename X, typename Y, typename Z, typename... Zs> auto vectors(X x, Y y, Z z, Zs... zs) { auto cont = vectors(y, z, zs...); return vector<decltype(cont)>(x, cont); }
template <class T> using reversed_priority_queue = priority_queue<T, vector<T>, greater<T> >;

int main() {
    constexpr int inf = 1e9+7;
    while (true) {
        // input
        int n, m, company, start, goal; scanf("%d%d%d%d%d", &n, &m, &company, &start, &goal); -- start; -- goal;
        if (n == 0) break;
        auto g = vectors(company, n, vector<pair<int, int> >());
        repeat (i, m) {
            int x, y, d, c; scanf("%d%d%d%d", &x, &y, &d, &c); -- x; -- y; -- c;
            g[c][x].emplace_back(y, d);
            g[c][y].emplace_back(x, d);
        }
        vector<int> p(company);
        repeat (c, company) {
            scanf("%d", &p[c]);
        }
        vector<vector<int> > q(company);
        vector<vector<int> > r(company);
        repeat (c, company) {
            q[c].resize(p[c] - 1);
            repeat (i, p[c] - 1) {
                scanf("%d", &q[c][i]);
            }
            r[c].resize(p[c]);
            repeat (i, p[c]) {
                scanf("%d", &r[c][i]);
            }
        }
        // solve
        // // initialize costs
        auto dist = vectors(company, n, n, inf);
        repeat (c, company) {
            repeat (i, n) {
                dist[c][i][i] = 0;
                for (auto e : g[c][i]) {
                    int j, d; tie(j, d) = e;
                    setmin(dist[c][i][j], d);
                }
            }
            repeat (k, n) repeat (i, n) repeat (j, n) { // Warshall-Floyd
                setmin(dist[c][i][j], dist[c][i][k] + dist[c][k][j]);
            }
        }
        auto cost = vectors(company, n, n, inf);
        repeat (c, company) {
            int max_dist = 0;
            repeat (i, n) repeat (j, n) if (dist[c][i][j] != inf) {
                setmax(max_dist, dist[c][i][j]);
            }
            vector<int> cost_at(max_dist + 3);
            int i = 0;
            repeat (d, cost_at.size() - 1) {
                if (i < p[c] - 1 and d == q[c][i]) ++ i;
                cost_at[d + 1] = cost_at[d] + r[c][i];
            }
            repeat (i, n) repeat (j, n) if (dist[c][i][j] != inf) {
                cost[c][i][j] = cost_at[dist[c][i][j]];
            }
        }
        // // Dijkstra
        vector<int> result(n, inf);
        vector<bool> fixed(n);
        reversed_priority_queue<pair<int, int> > que;
        result[start] = 0;
        que.emplace(0, start);
        while (not que.empty()) {
            int i = que.top().second; que.pop();
            if (fixed[i]) continue;
            fixed[i] = true;
            repeat (j, n) {
                int d = inf;
                repeat (c, company) {
                    setmin(d, result[i] + cost[c][i][j]);
                }
                if (d < result[j]) {
                    result[j] = d;
                    que.emplace(d, j);
                }
            }
        }
        // output
        printf("%d\n", result[goal] == inf ? -1 : result[goal]);
    }
    return 0;
}
```
