---
layout: post
redirect_from:
  - /writeup/algo/codeforces/634-e/
  - /blog/2016/02/29/cf-634-e/
date: 2016-02-29T08:18:38+09:00
tags: [ "competitive", "writeup", "codeforces", "graph", "tree" ]
---

# 8VC Venture Cup 2016 - Final Round (Div. 1 Edition) E. Preorder Test

この問題は好き。でも時間内には無理。

## [E. Preorder Test](http://codeforces.com/contest/634/problem/E)

### 問題

木が与えられる。頂点には重みが振ってある。
この木と同型な根付き木からdfsの順に$k$個頂点を取ってきたとき、その重みの最小値の最大値を答えよ。

### 解法

二分探索。$O(n \log n)$。

重みが$m$未満の頂点の使用を禁止して$k$個使えるかを判定する。
禁止頂点によって分けられた各連結成分ごとに見る。
各頂点ごとに、その頂点を始点とし、その部分木内で最大何個使えるかを再帰的に求める。
禁止頂点を含まない部分木はただで使えるが、禁止頂点に隣接した頂点をその連結成分内の根としておけば、親方向の部分木がただで使えることがなくなり楽である。

### 実装

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
template <class T> bool setmax(T & l, T const & r) { if (not (l < r)) return false; l = r; return true; }
using namespace std;
int main() {
    int n, k; cin >> n >> k;
    vector<int> a(n); repeat (i,n) cin >> a[i];
    vector<vector<int> > g(n);
    repeat (i,n-1) {
        int u, v; cin >> u >> v; -- u; -- v;
        g[u].push_back(v);
        g[v].push_back(u);
    }
    int low  = *min_element(a.begin(), a.end());
    int high = *max_element(a.begin(), a.end()) + 1; // [low, high)
    while (low + 1 < high) {
        int mid = (low + high) / 2;
        auto forbidden = [&](int i) { return a[i] < mid; };
        bool found = false;
        vector<bool> used(n);
        vector<int> dp(n);
        vector<bool> has_forbidden(n);
        repeat (i,n) if (not forbidden(i) and not used[i]) {
            int root = -1;
            function<void (int)> collect = [&](int i) {
                used[i] = true;
                for (int j : g[i]) if (not used[j]) {
                    if (not forbidden(j)) collect(j);
                    if (forbidden(j)) root = i;
                }
            };
            collect(i);
            if (root == -1) {
                found = true;
                break;
            }
            function<void (int, int)> dfs = [&](int i, int p) {
                if (found) return;
                int max_forbidden = 0;
                int snd_forbidden = 0;
                int sum_free = 0;
                for (int j : g[i]) if (j != p) {
                    if (forbidden(j)) {
                        has_forbidden[i] = true;
                    } else {
                        dfs(j, i);
                        if (found) return;
                        if (has_forbidden[j]) {
                            has_forbidden[i] = true;
                            setmax(snd_forbidden, dp[j]);
                            if (max_forbidden < snd_forbidden) swap(max_forbidden, snd_forbidden);
                        } else {
                            sum_free += dp[j];
                        }
                    }
                }
                dp[i] = 1 + sum_free + max_forbidden;
                if (k <= dp[i] + snd_forbidden) found = true;
            };
            dfs(root, -1);
            if (found) break;
        }
        (not found ? high : low) = mid;
    }
    cout << low << endl;
    return 0;
}
```
