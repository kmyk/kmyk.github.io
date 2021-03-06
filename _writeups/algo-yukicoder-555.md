---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/555/
  - /blog/2017/10/03/yuki-555/
date: "2017-10-03T03:56:23+09:00"
tags: [ "competitive", "writeup", "yukicoder", "dp" ]
"target_url": [ "https://yukicoder.me/problems/no/555" ]
---

# Yukicoder No.555 世界史のレポート

## solution

DP。ちょうど$i$文字書くのに必要な操作回数を$\mathrm{dp}\_i$とする。
配るDPをすれば$i$番目を見たとき$O(\frac{N}{i})$回の操作で済むので、全体で$O(N \log N)$。

$N$文字ちょうどである必要はないことに注意。$2N-1$文字分まで求める必要がある。
貰うDPでも$\sqrt{i}$まで見ればよいので$O(N \sqrt{N})$で解ける。

## implementation

``` c++
#include <algorithm>
#include <cstdio>
#include <vector>
#define repeat_from(i, m, n) for (int i = (m); (i) < int(n); ++(i))
using namespace std;
template <class T> inline void setmin(T & a, T const & b) { a = min(a, b); }

constexpr int inf = 1e9+7;
int main() {
    int n, c, v; scanf("%d%d%d", &n, &c, &v);
    vector<int> dp(2 * n + 1, inf);
    dp[1] = 0;
    repeat_from (x, 1, dp.size()) {
        for (int k = 1; x + k * x < dp.size(); ++ k) {
            setmin(dp[x + k * x], dp[x] + c + k * v);
        }
    }
    printf("%d\n", *min_element(dp.begin() + n, dp.end()));
    return 0;
}
```
