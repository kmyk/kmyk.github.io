---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/415/
  - /blog/2016/08/26/yuki-415/
date: "2016-08-26T23:45:04+09:00"
tags: [ "competitive", "writeup", "yukicoder", "gcd" ]
"target_url": [ "http://yukicoder.me/problems/no/415" ]
---

# Yukicoder No.415 ぴょん

## solution

gcd。$O(\log N)$。

移動できなくなったとき、次の移動先は必ず$0$である。そうでない場合を考えれば簡単に示せる。
つまり、$k = \min \\{ k \gt 0 \mid kD \equiv 0 \pmod N \\}$な$k$を求めればよい。$k-1$が答え。
$kD \equiv 0 \pmod N$は$\exists x \gt 0. kD = xN$と等しい。
$d = \mathrm{gcd}(N, D)$とすれば、kd\frac{D}{d} = xd\frac{N}{d}$であり、$k = \frac{N}{d}$を言うことができる。

## implementation

``` python
#!/usr/bin/env python3
import math
n, d = map(int,input().split())
print(n // math.gcd(n, d) - 1)
```
