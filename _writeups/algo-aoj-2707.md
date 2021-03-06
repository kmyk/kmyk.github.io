---
layout: post
redirect_from:
  - /writeup/algo/aoj/2707/
  - /blog/2017/04/04/aoj-2707/
date: "2017-04-04T18:25:26+09:00"
tags: [ "competitive", "writeup", "aoj", "binary-search" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2707" ]
---

# AOJ 2707: Jail

思考停止にぶたんをした。後からよく見たら$\log A$が取れた。

## solution

答えの上限$A = 10^{18}$が与えられてるので二分探索。$O(N \log A)$。
二分探索の形を良く見れば順方向に計算できて$O(N)$。

二分探索のため、答えが$m$以下であるかを判定する。
$m$から$m$以下の$k$の倍数の数を引くことを$n-1$回繰り返して$0$以下になるかを見ればよい。

具体的に式で書くと$m' = m - \lfloor \frac{m - 1}{k} \rfloor - 1$。
あるいは逆転させて$m = \lfloor \frac{km'}{k - 1} \rfloor + 1$とすれば直接計算できる。

## implementation

``` c++
#include <cstdio>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
using ll = long long;
using namespace std;
int main() {
    int n, k; scanf("%d%d", &n, &k);
    auto pred = [&](ll m) {
        repeat (i, n-1) {
            m -= (m - 1) / k + 1;
            if (m <= 0) return true;
        }
        return false;
    };
    ll l = 0, r = ll(1e18)+9;
    while (l + 1 < r) {
        ll m = (l + r) / 2;
        (pred(m) ? l : r) = m;
    }
    printf("%lld\n", l);
    return 0;
}
```

``` python
#!/usr/bin/env python3
n, k = map(int, input().split())
a = 0
for i in range(n-1):
    a = a * k // (k - 1) + 1
print(a)
```
