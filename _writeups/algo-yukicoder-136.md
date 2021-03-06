---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/136/
  - /blog/2016/08/26/yuki-136/
date: "2016-08-26T00:36:20+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/no/136" ]
---

# Yukicoder No.136 Yet Another GCD Problem

$\Sigma_i A_i = n$であるので答え$d = \mathrm{gcd}(A)$は$n$を割り切る$d \mid n$必要がある。
$l = \|A\|$に対し$2 \le l \le k$である必要がある。
これ以外に特に制約はなく、$d = \max \\{ d \mid (d \mid n) \land \exists l. (2 \le l \le k \land dl \le n) \\}$。
$\exists$は消去できて、$k$に依らず$d = \max \\{ d \mid (d \mid n) \land 2d \le n) \\}$でよい。

``` python
#!/usr/bin/env python3
n, k = map(int,input().split())
for d in range(n, 1-1, -1):
    if n % d == 0 and d * 2 <= n:
        print(d)
        break
```
