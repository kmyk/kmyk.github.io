---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/503/
  - /blog/2017/04/08/yuki-503/
date: "2017-04-08T02:42:06+09:00"
tags: [ "competitive", "writeup", "yukicoder", "dp" ]
"target_url": [ "http://yukicoder.me/problems/no/503" ]
---

# Yukicoder No.503 配列コレクション

## solution

DP。$O(N^2)$。

$N, K$を固定すれば操作終了後の配列の長さおよび操作回数は常に一定。それぞれ$n, r$と呼ぶこととする。
操作後の配列の要素は全て$D^k$の形をしていて、その指数部の総和は操作回数$r$に等しい。つまり$n$個の要素に$r$個を振り分けるようになっている。

DPで求める。配列の長さと指数部の総和からの関数$f$を$f(n,r)$が答えであるようにして、$f(i,j)$はそれぞれ$O(j)$で計算できる。
ただし$f$の計算には$f(i,j)$の条件を満たす配列の個数も必要で、これを隣で求めていく必要がある。重複組み合わせあたりで毎回計算してもよい。

## implementation

``` c++
#include <cstdio>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
using ll = long long;
using namespace std;
template <typename X, typename T> auto vectors(X x, T a) { return vector<T>(x, a); }
template <typename X, typename Y, typename Z, typename... Zs> auto vectors(X x, Y y, Z z, Zs... zs) { auto cont = vectors(y, z, zs...); return vector<decltype(cont)>(x, cont); }

const int mod = 1e9+7;
int solve(int n, int k, int d) {
    int r = 0; while (n >= k) { n -= k-1; r += 1; }
    if (d == 1) return n;
    auto dp  = vectors(n+1, r+1, 0);
    auto cnt = vectors(n+1, r+1, 0);
    dp[0][0] = 1;
    cnt[0][0] = 1;
    repeat (x,r) {
        dp[0][x+1] = dp[0][x] *(ll) d % mod;
        cnt[0][x+1] = 1;
    }
    repeat (y,n-1) {
        repeat (x,r+1) {
            ll acc = 0;
            ll cntacc = 0;
            repeat (z,x+1) {
                acc += dp[y][x-z] + cnt[y][x-z] *(ll) dp[0][z] % mod;
                cntacc += cnt[y][x-z];
            }
            dp[y+1][x] = acc % mod;
            cnt[y+1][x] = cntacc % mod;
        }
    }
    return dp[n-1][r];
}

int main() {
    int n, k, d; scanf("%d%d%d", &n, &k, &d);
    printf("%d\n", solve(n, k, d));
    return 0;
}
```
