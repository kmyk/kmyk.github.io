---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/390/
  - /blog/2016/07/08/yuki-390/
date: "2016-07-08T23:12:53+09:00"
tags: [ "competitive", "writeup", "yukicoder", "sieve-of-eratosthenes", "dp" ]
"target_url": [ "http://yukicoder.me/problems/no/390" ]
---

# Yukicoder No.390 最長の数列

これ好き

## solution

eratosthenesの篩っぽくDPすれば間に合う。$O(\Sigma \frac{1}{n}) = O(\log n)$。

$\mathrm{dp} : N \to N$を、ある数$i$を始点として数列を作ったときの最大長$\mathrm{dp}\_i$としてやればよい。
更新の漸化式は素直なものであるが、それを実際に行う際、着目している数$x$の倍数のみを見るようにする。つまりカウンタ変数`i`の更新を`i += x`とする。これは計算量を大きく落とす。

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <algorithm>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) y) { return (f)(begin(y), end(y), ## __VA_ARGS__); })(x)
using namespace std;
template <class T> void setmax(T & a, T const & b) { if (a < b) a = b; }
int main() {
    // input
    int n; scanf("%d", &n);
    vector<int> xs(n); repeat (i,n) scanf("%d", &xs[i]);
    // compute
    int l = *whole(max_element, xs);
    vector<bool> exists(l+1);
    for (int x : xs) exists[x] = true;
    vector<int> dp(l+1);
    whole(sort, xs);
    whole(reverse, xs);
    int ans = 0;
    for (int x : xs) {
        for (int y = x; y < l+1; y += x) {
            if (exists[y]) {
                setmax(dp[x], dp[y] + 1);
            }
        }
        setmax(ans, dp[x]);
    }
    // output
    printf("%d\n", ans);
    return 0;
}
```
