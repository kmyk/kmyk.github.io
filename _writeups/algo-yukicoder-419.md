---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/419/
  - /blog/2016/09/10/yuki-419/
date: "2016-09-10T00:22:13+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/no/418" ]
---

# Yukicoder No.419 直角三角形

$\sqrt{a^2 - b^2}$と$\sqrt{b^2 - a^2}$の扱いを`abs`でいい感じにしたつもりが、$a = b$の時の処理を見落としていたのでWA。

``` python
#!/usr/bin/env python3
import math
a, b = map(int,input().split())
print('%.10f' % math.sqrt(min(a**2+b**2, abs(a**2-b**2) if a != b else float('inf'))))
```
