---
layout: post
redirect_from:
  - /writeup/algo/codeforces/715-a/
  - /blog/2016/09/18/cf-715-a/
date: "2016-09-18T01:59:19+09:00"
tags: [ "competitive", "writeup", "codeforces" ]
"target_url": [ "http://codeforces.com/contest/715/problem/A" ]
---

# Codeforces Round #372 (Div. 1) A. Plus and Square Root

Bから解いた(しかもBを落とした)せいで点数減衰しててratingが溶けた。

## solution

According to the statement, when you push the $\sqrt{x}$ button, $x = m^2k^2(k+1)^2$ holds for some $m \ge 1$.
Fix $m = 1$, it's ok. Do it.

## implementation

``` python
#!/usr/bin/env python3
n = int(input())
x, k = 2, 1
while k <= n:
    y = ((k+1) * k) ** 2
    print((y - x) // k)
    x, k = (k+1) * k, k + 1
```
