---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/355/
  - /blog/2016/06/05/yuki-355/
date: 2016-06-05T19:49:34+09:00
tags: [ "competitive", "writeup", "yukicoder", "reactive" ]
"target_url": [ "http://yukicoder.me/problems/701" ]
---

# Yukicoder No.355 数当てゲーム(2)

## solution

クエリ数の制限は$100$である。
まず$x + y = 4$になるような(順序を無視した)組を見つける。
一番厳しいのは$x + y = 3$な組が分かっている状態から$x + y = 4$な組を得る遷移であるが、はずす数$4$通り入れる数$6$通りの$24$通りを試せばよいのでやればよい。制限は厳しくないので乱択で楽ができる。
あとはその順列の$4! = 24$通りを試せばよい。

## implementation

``` python
#!/usr/bin/env python3
import sys
import random
import itertools
def query(a, b, c, d):
    print(a, b, c, d)
    sys.stdout.flush()
    x, y = map(int,input().split())
    if x == 4 and y == 0:
        sys.exit(0)
    return x, y
a = { 0, 1, 2, 3 }
x, y = query(*a)
while x + y < 4:
    u = random.choice(list(a))
    v = random.choice(list(set(range(10)) - a))
    b = (a - { u }) | { v }
    nx, ny = query(*b)
    if x + y < nx + ny:
        a, x, y = b, nx, ny
for a in itertools.permutations(a):
    query(*a)
```
