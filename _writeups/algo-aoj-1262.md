---
layout: post
redirect_from:
  - /writeup/algo/aoj/1262/
  - /blog/2015/10/17/aoj-1262/
date: 2015-10-17T19:25:24+09:00
tags: [ "aoj", "acpc", "competitive", "writeup", "dp", "dijkstra", "dag" ]
"target_url": [ "!-- more --" ]
---

# AOJ 1262 Atomic Car Race

## [Atomic Car Race](http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1262) (ICPC Asia 2005 F) {#f}

### 問題

直線の道を車が走る。その車は走っていると速度が変化する。道の上にはチェックポイントがあって、そこでは時間を使ってタイヤを交換し速度の変化をリセットできる。道を走り抜けるための最小の時間を求める。

### 解法

dpあるいはdijkstra

### 解答

dpする。$O(a_n + n^2)$

``` c++
#include <iostream>
#include <cstdio>
#include <vector>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat(i,n) repeat_from(i,0,n)
using namespace std;
int main() {
    while (true) {
        int n; cin >> n;
        if (not n) break;
        vector<int> a(n); repeat (i,n) cin >> a[i];
        double b; cin >> b;
        int r; double v, e, f; cin >> r >> v >> e >> f;
        vector<double> ts(a.back()+1);
        ts[0] = 0;
        repeat (x,a.back()) {
            ts[x+1] = ts[x] + (x >= r
                ? 1 / (v - e *(double) (x - r))
                : 1 / (v - f *(double) (r - x)));
        }
        vector<double> dp(n+1);
        dp[0] = 0;
        repeat_from (j,1,n+1) dp[j] = ts[a[j-1]];
        repeat_from (i,1,n+1) {
            repeat_from (j,i+1,n+1) {
                int x = a[j-1] - a[i-1];
                dp[j] = min(dp[j], dp[i] + ts[x] + b);
            }
        }
        printf("%.6lf\n", dp[n]);
    }
    return 0;
}
```

<hr>

icpcに向けての練習会において解いた。
