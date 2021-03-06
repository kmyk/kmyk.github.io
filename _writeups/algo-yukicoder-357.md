---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/357/
  - /blog/2016/06/08/yuki-357/
date: 2016-06-08T05:12:57+09:00
tags: [ "competitive", "writeup", "yukicoder", "dp", "bit-dp" ]
"target_url": [ "http://yukicoder.me/problems/810" ]
---

# Yukicoder No.357 品物の並び替え (Middle)

典型。

## solution

bit DP。$O(N2^N)$。

$\operatorname{dp} : \mathcal{P}(\operatorname{Items}) \to \operatorname{Score}$という形で更新していく。

## implementation

``` c++
#include <iostream>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
template <class T> bool setmax(T & l, T const & r) { if (not (l < r)) return false; l = r; return true; }
using namespace std;
int main() {
    int n, m; cin >> n >> m;
    vector<vector<int> > score(n, vector<int>(n));
    repeat (i,m) {
        int x, y, z; cin >> x >> y >> z;
        score[x][y] = z;
    }
    vector<int> dp(1<<n);
    repeat (s,1<<n) {
        repeat (i,n) if (not (s & (1<<i))) {
            int acc = dp[s];
            repeat (j,n) if (s & (1<<j)) acc += score[j][i];
            setmax(dp[s | (1<<i)], acc);
        }
    }
    cout << dp[(1<<n)-1] << endl;
    return 0;
}
```
