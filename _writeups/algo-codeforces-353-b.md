---
layout: post
redirect_from:
  - /writeup/algo/codeforces/353-b/
  - /blog/2016/03/25/cf-353-b/
date: 2016-03-25T04:54:53+09:00
tags: [ "competitive", "writeup", "codeforces" ]
"target_url": [ "http://codeforces.com/contest/353/problem/B" ]
---

# Codeforces Round #205 (Div. 2) B. Two Heaps

おおまかな方針は見えやすいが、考察不足による実装ミスで悩んだ。

## 問題

$[10,99]$の範囲の整数が$2n$個与えられる。これらを$n$個ずつに分ける。
分割した結果$A,B$に関して、$\\{ f(a,b) \mid a \in A, b \in B \\}$が最大になるような分割を答えよ。
ただし$f(a,b)$は$a,b$の10進表現を文字列として結合し再度10進表現として解釈してできる整数。

## 解法

記号がたくさん与えられるので、それぞれに含まれる記号の種類の積が最大になるように分割する問題。
やる。

## 実装

1個しか含まれない記号は先に処理しておくとよい。
両方に同じ数配って余った残りの1個と同じ扱いをするとWAる。

``` python
#!/usr/bin/env python3
n = int(input())
a = list(map(int,input().split()))
c = [0] * 100
for i in a:
    c[i] += 1
x = [0] * 100
y = [0] * 100
j = 0
for i in range(100):
    if c[i] == 1:
        [x, y][j][i] += 1
        j = 1 - j
for i in range(100):
    if c[i] != 1:
        x[i] += c[i] // 2
        y[i] += c[i] // 2
        if c[i] % 2 == 1:
            [x, y][j][i] += 1
            j = 1 - j
xk = len(list(filter(lambda it: it, x)))
yk = len(list(filter(lambda it: it, y)))
print(xk * yk)
zs = [None] * (2*n)
for i in range(2*n):
    if x[a[i]] > 0:
        x[a[i]] -= 1
        zs[i] = 1
    else:
        assert y[a[i]] > 0
        y[a[i]] -= 1
        zs[i] = 2
print(*zs)
```
