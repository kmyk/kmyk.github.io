---
layout: post
redirect_from:
  - /writeup/algo/codeforces/336-b/
  - /blog/2015/12/25/cf-336-b/
date: 2015-12-25T20:25:17+09:00
tags: [ "competitive", "writeup", "codeforces" ]
---

# Codeforces Round #195 (Div. 2) B. Vasily the Bear and Fly

かなり読みにくく感じる問題文であった。問題としては難しいこともないはずだが、練習会で3人でそれぞれ解いて3人とも嵌ってしまった。

## [B. Vasily the Bear and Fly](http://codeforces.com/contest/336/problem/B) {#b}

### 問題

下図のように、半径$R$の円が$2 \times m$個並んでいる。

``` plain
  12345...        m

1 ooooooooooooooooo
2 ooooooooooooooooo
```

円$(1,i)$から円$(2,j)$まで円の内部(周上を含む)を通って移動するときの最短距離を$d\_{i,j}$とする。全ての$i,j \in \\{1\dots m\\}$に関して、$d\_{i,j}$の平均値を求めよ。

### 解説

$m \ge 3$のときに注意。円$(1,i)$と円$(1,i+1)$の接点から、円$(2,i+1)$と円$(2,i+2)$の接点へは、直線で移動できる。

### 実装

``` python
#!/usr/bin/env python3
from math import sqrt
m, r = map(int,input().split())
acc = 0.0
for i in range(m):
    acc += 2*r
    for j in [i,m-i-1]:
        if j:
            acc += (2 + sqrt(2)) * r
            acc += (2*(j-1)*j/2 + 2*sqrt(2)*(j-1)) * r
print('%.12f' % (acc/(m**2)))
```
