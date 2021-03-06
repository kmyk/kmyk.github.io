---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/253/
  - /blog/2016/09/29/yuki-253/
date: "2016-09-29T22:49:58+09:00"
tags: [ "competitive", "writeup", "yukicoder", "reactive", "binary-search" ]
"target_url": [ "http://yukicoder.me/problems/687" ]
---

# Yukicoder No.253 ロウソクの長さ

このところ茶会でいずさんに負け続けている。

## solution

$X$が小さいときに気を配りつつ、二分探索。

質問した回数$i$に対し$m-i$を聞くようにして二分探索。
ただし、最初の$1$回は$X \le 100$かどうか等を聞くようにする。
$\log{10^9} \lt 30$であるので普通に二分探索をすると$30$回ほど質問が必要となり、$X \le 30$のあたりの数を区別する質問をする前に$X = 0$に到達し詰むからである。

## implementation

``` python
#!/usr/bin/env python3
import sys
def ask(y):
    print('?', y)
    sys.stdout.flush()
    return int(input())
def ans(y):
    print('!', y)
    sys.exit()

m = 100
p = ask(m)
if p == 1:
    l = m
    r = 10**9 # (l, r]
elif p == -1:
    l = 10-1
    r = m-1
else:
    ans(m)
for i in range(1,100):
    m = (l+r+1) // 2
    p = ask(m-i)
    if p == 1:
        l = m
    elif p == -1:
        r = m
    else:
        ans(m)
```
