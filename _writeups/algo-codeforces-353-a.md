---
layout: post
redirect_from:
  - /writeup/algo/codeforces/353-a/
  - /blog/2016/03/25/cf-353-a/
date: 2016-03-25T04:54:49+09:00
tags: [ "competitive", "writeup", "codeforces" ]
"target_url": [ "http://codeforces.com/contest/353/problem/A" ]
---

# Codeforces Round #205 (Div. 2) A. Domino

## 問題

長さ$N$の整数の列$A,B$が与えられる。
$A_i$と$B_i$を交換する操作を複数回行なって、$\Sigma A$と$\Sigma B$を共に偶数にできるか。
できるならその最小回数を答えよ。

## 解法

偶奇。共に偶数にできる場合、交換は1回でよい。答えは$-1, 0, 1$のどれか。

## 実装

``` python
#!/usr/bin/env python3
n = int(input())
x = [None] * n
y = [None] * n
for i in range(n):
    x[i], y[i] = map(int,input().split())
sx = sum(x)
sy = sum(y)
ans = -1
if sx % 2 == 0 and sy % 2 == 0:
    ans = 0
elif sx % 2 == 1 and sy % 2 == 1:
    for i in range(n):
        if x[i] % 2 != y[i] % 2:
            ans = 1
print(ans)
```
