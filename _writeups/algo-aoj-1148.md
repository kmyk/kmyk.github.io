---
layout: post
redirect_from:
  - /writeup/algo/aoj/1148/
  - /blog/2017/07/12/aoj-1148/
date: "2017-07-12T20:25:48+09:00"
tags: [ "competitive", "writeup", "aoj", "icpc-domestic" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1148" ]
---

# AOJ 1148: ログイン/ログアウト記録の解析 / Analyzing Login/Logout Records

## solution

人数$M$も時刻$t \in \mathbb{N}$も少ないので愚直にやる。imos法っぽくやると楽だがそうでなくても間に合う。$T = \max t\_i - \min t\_i$として$O(r + MT + QT)$。

## implementation

``` c++
#include <cstdio>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_from(i, m, n) for (int i = (m); (i) < int(n); ++(i))
using namespace std;
template <typename X, typename T> auto vectors(X x, T a) { return vector<T>(x, a); }
template <typename X, typename Y, typename Z, typename... Zs> auto vectors(X x, Y y, Z z, Zs... zs) { auto cont = vectors(y, z, zs...); return vector<decltype(cont)>(x, cont); }

constexpr int max_t = 1260;
int main() {
    while (true) {
        int n, m; scanf("%d%d", &n, &m);
        if (n == 0 and m == 0) break;
        auto login = vectors(m, max_t + 2, int());
        int r; scanf("%d", &r);
        while (r --) {
            int t, i, j, s; scanf("%d%d%d%d", &t, &i, &j, &s); -- i; -- j;
            login[j][t] += s ? +1 : -1;
        }
        repeat (j, m) {
            repeat (t, max_t + 1) {
                login[j][t + 1] += login[j][t];
            }
        }
        int q; scanf("%d", &q);
        while (q --) {
            int l, r, j; scanf("%d%d%d", &l, &r, &j); -- j;
            int result = 0;
            repeat_from (t, l, r) {
                result += bool(login[j][t]);
            }
            printf("%d\n", result);
        }
    }
    return 0;
}
```
