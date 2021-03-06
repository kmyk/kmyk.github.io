---
layout: post
redirect_from:
  - /writeup/algo/aoj/1277/
  - /blog/2017/07/28/aoj-1277/
date: "2017-07-28T21:02:50+09:00"
tags: [ "competitive", "writeup", "aoj", "icpc", "icpc-asia", "dp" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1277" ]
---

# AOJ 1277. Minimal Backgammon

`L`や`B`のときに発生する効果は即時であり、`B`なマスから移動するときその代わりに振り出しへ飛ぶのではない。それはそう。

## solution

愚直にDP。$O(nt)$。

## implementation

``` c++
#include <cstdio>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_from(i, m, n) for (int i = (m); (i) < int(n); ++(i))
using namespace std;
template <typename X, typename T> auto vectors(X x, T a) { return vector<T>(x, a); }
template <typename X, typename Y, typename Z, typename... Zs> auto vectors(X x, Y y, Z z, Zs... zs) { auto cont = vectors(y, z, zs...); return vector<decltype(cont)>(x, cont); }

int main() {
    while (true) {
        int n, t, l, b; scanf("%d%d%d%d", &n, &t, &l, &b);
        if (n == 0 and t == 0 and l == 0 and b == 0) break;
        vector<char> f(n + 1);
        repeat (i, l) { int x; scanf("%d", &x); f[x] = 'L'; }
        repeat (i, b) { int x; scanf("%d", &x); f[x] = 'B'; }
        auto dp = vectors(t + 1, n + 1, double());
        dp[0][0] = 1;
        double result = 0;
        repeat (y, t + 1) {
            result += dp[y][n];
            dp[y][n] = 0;
            repeat (x, n + 1) {
                int ny = y + (f[x] == 'L' ? 2 : 1);
                if (ny >= t + 1) continue;
                repeat_from (dice, 1, 6 + 1) {
                    int nx = x + dice;
                    if (nx > n) nx = n - (nx - n);
                    if (f[nx] == 'B') nx = 0;
                    dp[ny][nx] += dp[y][x] / 6;
                }
            }
        }
        printf("%.8lf\n", result);
    }
    return 0;
}
```
