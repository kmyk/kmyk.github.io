---
layout: post
redirect_from:
  - /writeup/algo/codeforces/626-a/
  - /blog/2016/02/14/cf-626-a/
date: 2016-02-14T05:27:05+09:00
tags: [ "competitive", "writeup", "codeforces" ]
---

# 8VC Venture Cup 2016 - Elimination Round A. Robot Sequence

こどふぉでは珍しい気がする問題。
企業コンテストだから普段のこどふぉと傾向が違うのかな。

## [A. Robot Sequence](http://codeforces.com/contest/626/problem/A)

全部試す。$O(n^2)$。

``` python
#!/usr/bin/env python3
n = int(input())
s = input()
ans = 0
for l in range(n-1):
    for r in range(l+1,n+1):
        y, x = 0, 0
        for c in s[l:r]:
            if   c == 'U': y += 1
            elif c == 'D': y -= 1
            elif c == 'R': x += 1
            elif c == 'L': x -= 1
        if y == 0 and x == 0:
            ans += 1
print(ans)
```
