---
layout: post
redirect_from:
  - /writeup/algo/aoj/0537/
  - /blog/2016/03/29/aoj-0537/
date: 2016-03-29T01:15:01+09:00
tags: [ "competitive", "writeup", "aoj", "dp", "joi" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=0537" ]
---

# AOJ 0537 Bingo

更新回数と空間を入れ換える問題。
おそらくは空間計算量が減ったからなのだろうが、TLEする実装が何故TLEするのか、少し釈然としない。
でも面白かったとは感じる。

## 解法

$N^2 \times S$のdp。$O(N^2MS)$。

問題は、$[1,M]$の範囲の整数からなる$N^2$個の狭義単調増加な列で総和が$S$なものを数える、と整理できる。
ここで単純に、増加列を前から埋めていき最後に使った数$i$と列の総和$j$から$dp\_{i,j}$とする$M \times S$のdpが思い付く。
これは$O(N^2MS)$であるが、実装すると定数倍$K = 2,3$間に合わない。

そうではなくて、$[1,M]$から$N^2$個選んで総和を$S$にすると解釈する。
$M$個の使用可能な整数を順に使うか使わないか決めていき、使った数の数$i$と列の総和$j$から$dp\_{i,j}$とする$N^2 \times S$のdpをする。
これも同様に$O(N^2MS)$であるが、実装すると間に合う。

## 実装

``` c++
#include <iostream>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_reverse(i,n) for (int i = (n)-1; (i) >= 0; --(i))
using namespace std;
const int mod = 100000;
int main() {
    while (true) {
        int n, m, s; cin >> n >> m >> s;
        if (n == 0 and m == 0 and s == 0) break;
        vector<vector<int> > dp(n*n+1, vector<int>(s+1));
        dp[0][0] = 1;
        repeat (i,m) {
            repeat_reverse (j,n*n) {
                repeat (k,s-i) {
                    dp[j+1][k+(i+1)] += dp[j][k];
                    dp[j+1][k+(i+1)] %= mod;
                }
            }
        }
        cout << dp[n*n][s] << endl;
    }
    return 0;
}
```

### TLE

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
using namespace std;
const int mod = 100000;
int main() {
    while (true) {
        int n, m, s; cin >> n >> m >> s;
        if (n == 0 and m == 0 and s == 0) break;
        vector<vector<int> > cur(s+1, vector<int>(m+1));
        vector<vector<int> > prv(s+1, vector<int>(m+1));
        cur[0][0] = 1;
        repeat (i,n*n) {
            prv.swap(cur);
            repeat (j,s+1) {
                cur[j][0] = 0;
                repeat (k,min(m,j)) {
                    cur[j][k+1] = (cur[j-1][k] + prv[j-k-1][k]) % mod;
                }
            }
        }
        int ans = accumulate(cur[s].begin(), cur[s].end(), 0ll) % mod;
        cout << ans << endl;
    }
    return 0;
}
```
