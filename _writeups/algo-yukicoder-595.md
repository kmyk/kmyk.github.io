---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/595/
  - /blog/2017/12/08/yuki-595/
date: "2017-12-08T07:40:08+09:00"
tags: [ "competitive", "writeup", "yukicoder", "dp" ]
"target_url": [ "https://yukicoder.me/problems/no/595" ]
---

# Yukicoder No.595 登山

## solution

DP。$O(N)$。

引き返すのは一般に損であることと開始位置が$1$であることにより、地点$1, 2, \dots, N$を適当な$k$個の区間に分割し$k - 1$回のワープを使ってそれぞれの区間を左から右(あるいは右から左)へまっすぐ移動するのが最適。ワープするときに距離は無関係なので左から順に決めていけばよく、地点$i$まで全て訪問したときのエネルギーの最小値を$\mathrm{dp}(i)$としてDPすればよい。

## implementation

``` c++
#include <cstdio>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
using ll = long long;
using namespace std;
template <class T> inline void setmin(T & a, T const & b) { a = min(a, b); }

constexpr ll inf = ll(1e18)+9;
int main() {
    // input
    int n, p; scanf("%d%d", &n, &p);
    vector<int> h(n); repeat (i, n) scanf("%d", &h[i]);
    // solve
    ll fwd = 0;
    ll bck = inf;
    repeat (i, n - 1) {
        ll prv = min(fwd, bck);
        if (h[i] < h[i + 1]) {
            fwd += h[i + 1] - h[i];
        } else {
            bck += h[i] - h[i + 1];
        }
        setmin(fwd, prv + p);
        setmin(bck, prv + p);
    }
    ll result = min(fwd, bck);
    // output
    printf("%lld\n", result);
    return 0;
}
```
