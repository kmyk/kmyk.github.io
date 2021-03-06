---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/366/
  - /blog/2016/04/30/yuki-366/
date: 2016-04-30T00:41:31+09:00
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/1077" ]
---

# Yukicoder No.366 ロボットソート

## solution

$\bmod K$で分けてbubble sort。長さ $N/K$ の列のソートを $K$ 回やるので $O(N^2/K)$。

$i$番目のものは$j = i \pmod K$であるような$j$番目のものとしか交換できない。
よって列を$K$本の列に分ける。
特に、それらの中で隣合う要素としか交換できないので、これはbubble sortの交換回数を数える問題になる。
ただし、各列でのsortが大域的に見てsortになっていなければ失敗。

## implementation

``` python
#!/usr/bin/env python3
# input
n, k = map(int,input().split())
xs = list(map(int,input().split()))
# compress
ys = list(range(n))
ys.sort(key=lambda y: xs[y])
zs = [None] * n
for i in range(n):
    zs[ys[i]] = i
# sort
ans = 0
yss = [zs[i::k] for i in range(k)]
for i, ys in enumerate(yss):
    for j, y in enumerate(ys):
        ans += len(list(filter(lambda x: y < x, ys[:j]))) # bubble sort locally
    ys.sort()
    if ys != list(range(i,n,k))[:len(ys)]:
        ans = -1 # failure globally
        break
print(ans)
```
