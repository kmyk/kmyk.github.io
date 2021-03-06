---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/402/
  - /blog/2016/07/24/yuki-402/
date: "2016-07-24T00:14:26+09:00"
tags: [ "competitive", "writeup", "yukicoder", "bfs" ]
"target_url": [ "http://yukicoder.me/problems/no/402" ]
---

# Yukicoder No.402 最も海から遠い場所

周囲の海を$1$マス分 陽に持ってそこからBFSをするとよい。
この制約でコンテスト中に最大正方形まで思い付く人はすごい。

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <queue>
#include <tuple>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
using namespace std;
template <typename T, typename X> auto vectors(T a, X x) { return vector<T>(x, a); }
template <typename T, typename X, typename Y, typename... Zs> auto vectors(T a, X x, Y y, Zs... zs) { auto cont = vectors(a, y, zs...); return vector<decltype(cont)>(x, cont); }
int main() {
    // input
    int h, w; scanf("%d%d", &h, &w);
    vector<vector<bool> > is_sea = vectors<bool>(false, h+2, w+2);
    repeat_from (y,1,h+1) {
        repeat_from (x,1,w+1) {
            char c; scanf(" %c", &c);
            is_sea[y][x] = c == '#';
        }
    }
    // compute
    vector<vector<int> > dist = vectors<int>(-1, h+2, w+2);
    queue<pair<int,int> > que;
    repeat (y,h+2) {
        repeat (x,w+2) {
            if (not is_sea[y][x]) {
                dist[y][x] = 0;
                que.emplace(y, x);
            }
        }
    }
    int ans = -1;
    while (not que.empty()) {
        int y, x; tie(y, x) = que.front(); que.pop();
        ans = dist[y][x];
        for (int dy : { -1, 0, 1 }) {
            for (int dx : { -1, 0, 1 }) {
                int ny = y + dy;
                int nx = x + dx;
                if (ny < 0 or h+2 <= ny or nx < 0 or w+2 <= nx) continue;
                if (dist[ny][nx] == -1) {
                    dist[ny][nx] = dist[y][x] + 1;
                    que.emplace(ny, nx);
                }
            }
        }
    }
    // output
    printf("%d\n", ans);
    return 0;
}
```
