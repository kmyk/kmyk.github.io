---
layout: post
redirect_from:
  - /writeup/algo/aoj/1169/
  - /blog/2017/06/30/aoj-1169/
date: "2017-06-30T13:51:06+09:00"
tags: [ "competitive", "writeup", "aoj", "icpc-domestic", "graph", "dijkstra" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1169" ]
---

# AOJ 1169: 最強の呪文 / The Most Powerful Spell

想定解は強連結成分分解して上手く辺を落とした後に貪欲とかなんじゃないかなと思いながら解いたが、そうではなかった。

## solution

雑にDijkstra法。なんだか無限loopっぽい感じなら`NO`にしておく。label長の最大値を$l$として$O(n^2 l^2 \log n l)$ぐらい。

最短経路をDijkstraで求める。通常ではある点の最短経路が求まった後にその点を再度見ることはないが、今回はある種の負閉路があるためさらに更新する。
何も考えずに繰り返すと終点への最短路に関与しない部分の負閉路に処理をblockされることがあるので、適当な長さで打ち切る。
有効な最短経路ならlabel長の最大値を$l$として長さは$nl$以下なので、それを越えるなら打ち切ってよい。
そのようにすると本来負閉路があって終点には辿り着けない場合でも終点への最短路候補が与えられてしまうが、これもlabel長の最大値を越えているかどうかで判断すればよい。
打ち切りの際の注意としては、打ち切りする長さを越えた値で更新した直後はまだ隣接頂点をqueueに追加する必要があること。

## implementation

``` c++
#include <algorithm>
#include <iostream>
#include <queue>
#include <tuple>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f, x, ...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using namespace std;
template <class T> inline void setmax(T & a, T const & b) { a = max(a, b); }
template <typename X, typename T> auto vectors(X x, T a) { return vector<T>(x, a); }
template <typename X, typename Y, typename Z, typename... Zs> auto vectors(X x, Y y, Z z, Zs... zs) { auto cont = vectors(y, z, zs...); return vector<decltype(cont)>(x, cont); }
template <class T> using reversed_priority_queue = priority_queue<T, vector<T>, greater<T> >;

bool is_prefix(string const & a, string const & b) {
    return a.compare(0, b.length(), b);
}
int main() {
    while (true) {
        // input
        int n, edges, start, goal; cin >> n >> edges >> start >> goal;
        if (n == 0) break;
        auto label = vectors(n, n, vector<string>());
        vector<vector<int> > g(n);
        int label_length_max = 0;
        repeat (i, edges) {
            int x, y; string s; cin >> x >> y >> s;
            label[x][y].push_back(s);
            setmax<int>(label_length_max, s.length());
            g[x].push_back(y);
        }
        repeat (x, n) {
            whole(sort, g[x]);
            g[x].erase(whole(unique, g[x]), g[x].end());
        }
        // solve
        string result;
        vector<string> dist(n);
        reversed_priority_queue<tuple<string, int, int> > que;
        dist[start] = "";
        que.emplace(dist[start], -1, start);
        while (not que.empty()) {
            string dist_x; int z, x; tie(dist_x, z, x) = que.top(); que.pop();
            if (label_length_max * (n + 3) < dist[x].length()) continue;
            if (z != -1 and dist[x] + "\xff" < dist_x) continue;
            if (z != -1 and not is_prefix(dist[x], dist_x)) continue;
            if (x == goal) {
                if (result.empty()) {
                    result = dist_x;
                } else if (dist_x < result) {
                    result = "NO";
                    break;
                } else {
                    continue;
                }
            }
            dist[x] = dist_x;
            for (int y : g[x]) {
                for (string delta : label[x][y]) {
                    if (dist[x] + delta < dist[y] + "\xff") {
                        que.emplace(dist[x] + delta, x, y);
                    }
                }
            }
        }
        // output
        if (dist[goal].empty() or label_length_max * n < dist[goal].length()) {
            dist[goal] = "NO";
        }
        cout << dist[goal] << endl;
    }
    return 0;
}
```
