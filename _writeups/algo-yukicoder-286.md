---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/286/
  - /blog/2016/07/19/yuki-286/
date: "2016-07-19T18:33:09+09:00"
tags: [ "competitive", "writeup", "yukicoder", "dp", "bit-dp" ]
"target_url": [ "http://yukicoder.me/problems/no/286" ]
---

# Yukicoder No.286 Modulo Discount Store

教科書的なbit-DPであった。

## solution

bit-DP。$O(N2^N)$。

既に購入した商品の集合からそのために支払った金額の最小値を返す関数$f : \mathcal{P}(N) \to \mathbb{N}$を更新する。

## implementation

``` c++
#include <cstdio>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
using namespace std;
template <class T> void setmin(T & a, T const & b) { if (b < a) a = b; }
const int inf = 1e9+7;
const int K = 1000;
int main() {
    int n; scanf("%d", &n);
    vector<int> m(n); repeat (i,n) scanf("%d", &m[i]);
    vector<int> dp(1<<n, inf);
    dp[0] = 0;
    repeat (s,1<<n) {
        int acc = 0; repeat (i,n) if (s & (1<<i)) acc += m[i];
        repeat (i,n) if (s & (1<<i)) {
            int t = s & ~ (1<<i);
            setmin(dp[s], dp[t] + max(0, m[i] - (acc - m[i]) % K));
        }
    }
    printf("%d\n", dp[(1<<n) - 1]);
    return 0;
}
```
