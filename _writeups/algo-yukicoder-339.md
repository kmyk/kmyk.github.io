---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/339/
  - /blog/2016/05/27/yuki-339/
date: 2016-05-27T15:26:29+09:00
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/776" ]
---

# Yukicoder No.339 何人が回答したのか

与えられるのは$0.01$から$1.00$までの3桁固定小数点数である。
人数は整数なので、そのそれぞれに関して整数にするために掛ける必要のある整数がある。
これはgcdを使って求まる。
それらのlcmを取ればよい。

``` python
#!/usr/bin/env python3
from functools import reduce
import math
def lcm(a,b): return a * b // math.gcd(a, b)
n = int(input())
xs = [int(input()) for i in range(n)]
ans = reduce(lcm, [100 // math.gcd(100, x) for x in xs])
print(ans)
```
