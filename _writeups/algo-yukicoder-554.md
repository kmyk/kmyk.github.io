---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/554/
  - /blog/2017/10/03/yuki-554/
date: "2017-10-03T03:56:21+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "https://yukicoder.me/problems/no/554" ]
---

# Yukicoder No.554 recurrence formula

$n \le 10^5$だし定数倍高速化$O(n^2)$も通る気がする。

## solution

$a\_1$から順に求めていく。
愚直に漸化式に従うと$O(n^2)$。
$k$未満の偶数番目の項の総和と奇数番目の項の総和をそれぞれ持っておけば$a\_k$が求まるので、そのようにすれば$O(n)$。

## implementation

``` python
#!/usr/bin/env python3
mod = 10 ** 9 + 7
n = int(input())
a = [ None ] * (n + 1)
a[0] = 0
a[1] = 1
acc = [ 0, 1 ]
for i in range(2, n + 1):
    a[i] = i * acc[1 - i % 2] % mod
    acc[i % 2] += a[i]
print(a[n])
```
