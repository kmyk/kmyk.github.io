---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/242/
  - /blog/2016/08/26/yuki-242/
date: "2016-08-26T01:16:45+09:00"
tags: [ "competitive", "writeup", "yukicoder", "expected-value", "linearity" ]
"target_url": [ "http://yukicoder.me/problems/no/242" ]
---

# Yukicoder No.242 ビンゴゲーム

期待値の線形性。今回の$12$本の列は全て独立であるかのように扱え、$e = 12 \cdot \frac{n}{99} \cdot \frac{n-1}{98} \cdot \frac{n-2}{97} \cdot \frac{n-3}{96} \cdot \frac{n-4}{95}$が答え。

``` c++
#include <cstdio>
using namespace std;
int main() {
    int n; scanf("%d", &n);
    double e = n/99. * (n-1)/98. * (n-2)/97. * (n-3)/96. * (n-4)/95.;
    printf("%.8lf\n", e * 12);
    return 0;
}
```
