---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/479/
  - /blog/2017/04/20/yuki-479/
date: "2017-04-20T03:27:56+09:00"
tags: [ "competitive", "writeup", "yukicoder", "graph" ]
"target_url": [ "http://yukicoder.me/problems/no/479" ]
---

# Yukicoder No.479 頂点は要らない

グラフの図があり丁寧でよい。

## solution

番号の小さい頂点から辺を使い尽くすまで貪欲に全て使い、その後番号の大きい頂点から不必要なら外していく。$O(N + M)$。

$2$進数どうこうというのは、使うか使わないかがbitに対応すると考えれば使った頂点番号を逆順に整列して辞書順最小といいなおせる。
これにより$i$番目を使うのより$\\{ j \mid j \lt i \\}$の全てを使う方が有利であり、まずは貪欲に使う。
辺を使い尽した後もだいたい同様。

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <algorithm>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_reverse(i,n) for (int i = (n)-1; (i) >= 0; --(i))
#define whole(f,x,...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using namespace std;
int main() {
    // input
    int n, m; scanf("%d%d", &n, &m);
    vector<int> a(m), b(m); repeat (i,m) scanf("%d%d", &a[i], &b[i]);
    // prepare list
    vector<vector<int> > g(n);
    repeat (i,m) {
        g[a[i]].push_back(i);
        g[b[i]].push_back(i);
    }
    // solve
    // // greedy
    vector<bool> result(n);
    vector<int> used(m);
    int count_used = 0;
    repeat (j,n) {
        result[j] = true;
        for (int i : g[j]) {
            if (used[i] == 0) count_used += 1;
            used[i] += 1;
        }
        if (count_used == m) break;
    }
    // // remove unnecessary vertices
    repeat_reverse (j,n) if (result[j]) {
        bool is_removable = true;
        for (int i : g[j]) {
            if (used[i] == 1) is_removable = false;
        }
        if (is_removable) {
            result[j] = false;
            for (int i : g[j]) {
                used[i] -= 1;
            }
        }
    }
    // output
    while (result.size() >= 2 and result.back() == false) result.pop_back();
    whole(reverse, result);
    for (bool p : result) printf("%d", p);
    printf("\n");
    return 0;
}
```
