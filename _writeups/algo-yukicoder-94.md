---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/94/
  - /blog/2016/02/18/yuki-94/
date: 2016-02-18T23:54:47+09:00
tags: [ "competitive", "writeup", "yukicoder", "dfs", "euclidean-distance" ]
---

# Yukicoder No.94 圏外です。(EASY)

サンプルがあったので投げたら、ひどい実装忘れをしていてWAした。

## [No.94 圏外です。(EASY)](http://yukicoder.me/problems/103)

### 解法

$O(N^2)$。

中継局に関して通信可能性でグラフを作り、同じ連結成分に含まれる2頂点の(元の空間上の直線)距離の最大値を求め、$2$を足せばよい。

中継局が無い場合は特殊ケース。

### 実装

この問題にunion-find木はまったく不要であることに注意。
連結成分を求めたいだけであるので、単にdfsすればよい。
一般に、グラフへの辺の動的な追加が発生しない限りunion-find木は不要である。

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>
#include <cstdio>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
template <typename T> bool setmax(T & l, T const & r) { if (not (l < r)) return false; l = r; return true; }
using namespace std;
int sq(int x) { return x * x; }
int main() {
    int n; cin >> n;
    vector<int> x(n), y(n); repeat (i,n) cin >> x[i] >> y[i];
    if (n == 0) {
        cout << 1 << endl;
        return 0;
    }
    vector<vector<bool> > g(n, vector<bool>(n));
    repeat (i,n) repeat (j,n) {
        g[i][j] = (sq(y[j] - y[i]) + sq(x[j] - x[i]) <= 100);
    }
    vector<bool> used(n);
    function<void (int, vector<int> &)> dfs = [&](int i, vector<int> & acc) {
        used[i] = true;
        acc.push_back(i);
        repeat (j,n) if (g[i][j] and not used[j]) dfs(j, acc);
    };
    double ans = 0;
    repeat (i,n) if (not used[i]) {
        vector<int> acc;
        dfs(i, acc);
        for (int j : acc) for (int k : acc) {
            setmax(ans, sqrt(sq(y[k] - y[j]) + sq(x[k] - x[j])));
        }
    }
    printf("%.12lf\n", ans + 2);
    return 0;
}
```
