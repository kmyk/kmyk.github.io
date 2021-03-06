---
layout: post
redirect_from:
  - /writeup/algo/codeforces/601-a/
  - /blog/2015/11/25/cf-601-a/
date: 2015-11-25T04:11:41+09:00
tags: [ "competitive", "writeup", "codeforces", "graph", "shortest-path", "complete-graph" ]
---

# Codeforces Round #333 (Div. 1) A. The Two Routes

けっこう簡単、なはずなのだけど、本番は頭回ってなかったのでA\*法とか書いてしまった。
サンプルをきっちり図に書いていれば防げたであろう悲劇。

<!-- more -->

## [A. The Two Routes](http://codeforces.com/contest/601/problem/A) {#a}

### 問題

町が$n$個ある。町と町を繋ぐ鉄道が$m$本ある。町と町の間に鉄道がない場合は道路がある。
町$1$から$n$まで、電車とバスが同時に移動する。
電車とバスは、町$1,n$は除いて、同時に同じ町に存在できない。
電車とバスの両方が町$n$に着くのに必要な時間の最小値を求めよ。

### 解法

任意の町と町に鉄道あるいは道路があるので、これは$2$-辺彩色された完全グラフである。
電車あるいはバスのどちらか一方は時間$1$で町$n$に到着可能である。
後はもう一方が要する時間を求めればよく、これは(無彩色の)単純グラフ上の最短路問題である。

### 実装

``` c++
#include <iostream>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
using namespace std;
int shortest_distance(int n, vector<vector<bool> > const & g, bool p) {
    vector<vector<int> > dist(n, vector<int>(n, 1000000007));
    repeat (i,n) dist[i][i] = 0;
    repeat (i,n) repeat (j,n) if (g[i][j] == p) dist[i][j] = 1;
    repeat (k,n) { // warshall floyd
        repeat (i,n) {
            repeat (j,n) {
                dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
            }
        }
    }
    return dist[0][n-1];
}
int main() {
    int n, m; cin >> n >> m;
    vector<vector<bool> > has_railway(n, vector<bool>(n));
    repeat (i,m) {
        int u, v; cin >> u >> v;
        -- u; -- v;
        has_railway[u][v] = true;
        has_railway[v][u] = true;
    }
    int train = shortest_distance(n, has_railway, true);
    int bus   = shortest_distance(n, has_railway, false);
    if (train == 1000000007 or bus == 1000000007) {
        cout << -1 << endl;
    } else {
        cout << max(train, bus) << endl;
    }
    return 0;
}
```
