---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/455/
  - /blog/2016/12/07/yuki-455/
date: "2016-12-07T14:03:36+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/no/455" ]
---

# Yukicoder No.455 冬の大三角

一直線上にあるかどうかは外積で判定できる。
乱択をして判定なしで答えてもも失敗確率は$0.5$未満であるので、(テストケース次第ではあるが)何度も提出すれば通る気がする。

``` python
#!/usr/bin/env python3
h, w = map(int, input().split())
s = [ input() for _ in range(h) ]
stars = []
for y in range(h):
    for x in range(w):
        if s[y][x] == '*':
            stars += [( y, x )]
assert len(stars) == 2
for y in range(h):
    for x in range(w):
        if len(stars) == 3:
            continue
        if s[y][x] == '-':
            ay, ax = stars[0]
            by, bx = stars[1]
            if (ay - y) * (bx - x) != (ax - x) * (by - y):
                s[y] = list(s[y])
                s[y][x] = '*'
                s[y] = ''.join(s[y])
                stars += [( y, x )]
for line in s:
    print(line)
```
