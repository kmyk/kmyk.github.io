---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/340/
  - /blog/2016/05/27/yuki-340/
date: 2016-05-27T17:44:15+09:00
tags: [ "competitive", "writeup", "yukicoder", "binary-indexed-tree" ]
"target_url": [ "http://yukicoder.me/problems/932" ]
---

# Yukicoder No.340 雪の足跡

普通に幅優先探索すれば$O(HW)$でしょと思って書いたら、それは間違ってはいなかったが、グラフの構築に$O(NM \max \\{ H, W \\})$かかっていてTLEした。

## solution

グリッドグラフをsegment treeやbinary indexed treeで持ち、この上で単純な幅優先探索。$O((NM + HW) \log \max \\{ H, W \\})$。

区画を頂点、移動可能性を辺とするグリッドグラフを考える。
これを単純に構築すると、空間は間に合うが、時間が間に合わない。
そこで、グリッドを行と列ごとに分解し、区間に対する一様加算を用いて構築する。
segment木などを使い、imos法の要領で辺群を追加していく。

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <set>
#include <queue>
#include <functional>
#include <cstdint>
#include <cassert>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
using namespace std;

template <typename T>
struct binary_indexed_tree { // on monoid
    vector<T> a;
    T unit;
    function<T (T,T)> append; // associative
    template <typename F>
    binary_indexed_tree(size_t n, T a_unit, F a_append) : a(n, a_unit), unit(a_unit), append(a_append) {}
    void point_append(size_t i, T w) { // a[i] += w
        for (size_t j = i+1; j <= a.size(); j += j & -j) a[j-1] = append(a[j-1], w);
    }
    int initial_range_concat(size_t i) { // sum [0, i)
        T acc = unit;
        for (size_t j = i; 0 < j; j -= j & -j) acc = append(acc, a[j-1]);
        return acc;
    }
};

const int dy[4] = { -1, 1, 0, 0 };
const int dx[4] = { 0, 0, 1, -1 };
struct state_t { int dist; int y, x; };
int main() {
    int w, h, n; scanf("%d%d%d", &w, &h, &n);
    vector<binary_indexed_tree<int> > row(h, binary_indexed_tree<int>(w, 0, plus<int>()));
    vector<binary_indexed_tree<int> > col(w, binary_indexed_tree<int>(h, 0, plus<int>()));
    repeat (i,n) {
        int m; scanf("%d", &m);
        vector<int> b(m+1); repeat (i,m+1) scanf("%d", &b[i]);
        repeat (i,m) {
            int y0 = b[i]   / w;
            int y1 = b[i+1] / w;
            int x0 = b[i]   % w;
            int x1 = b[i+1] % w;
            if (y0 == y1) {
                int y = y0;
                int xl = min(x0,x1);
                int xr = max(x0,x1);
                row[y].point_append(xl, +1);
                row[y].point_append(xr, -1);
            } else if (x0 == x1) {
                int x = x0;
                int yl = min(y0,y1);
                int yr = max(y0,y1);
                col[x].point_append(yl, +1);
                col[x].point_append(yr, -1);
            } else {
                assert (false);
            }
        }
    }
    vector<vector<int> > dist(h, vector<int>(w));
    queue<state_t> que;
    dist[0][0] = 1;
    que.push((state_t) { 1, 0, 0 });
    while (not que.empty()) {
        state_t s = que.front(); que.pop();
        repeat (i,4) {
            state_t t;
            t.dist = s.dist + 1;
            t.y = s.y + dy[i];
            t.x = s.x + dx[i];
            if (t.y < 0 or h <= t.y or t.x < 0 or w <= t.x) continue;
            if (i == 0 or i == 1) { // up, down
                if (not col[t.x].initial_range_concat(max(s.y, t.y))) continue;
            } else if (i == 2 or i == 3) { // right, left
                if (not row[t.y].initial_range_concat(max(s.x, t.x))) continue;
            }
            if (dist[t.y][t.x]) continue;
            dist[t.y][t.x] = t.dist;
            que.push(t);
        }
    }
    if (dist[h-1][w-1]) {
        printf("%d\n", dist[h-1][w-1] - 1);
    } else {
        puts("Odekakedekinai..");
    }
    return 0;
}
```
