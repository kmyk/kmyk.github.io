---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/443/
  - /blog/2016/11/12/yuki-443/
date: "2016-11-12T19:56:22+09:00"
tags: [ "competitive", "writeup", "yukicoder", "gcd" ]
"target_url": [ "http://yukicoder.me/problems/no/443" ]
---

# Yukicoder No.443 GCD of Permutation

乱択解を投げてみたら通った。
不正っぽい通しかたするのは好きだけど問題としては良くないよねと思ったが、想定解はけっこう面白いものだった。
後から落とされた。

## solution

### 想定

互換して差を見る。$O(N)$。

整数$n$の$10$進展開末尾の$2$数字$a, b$を交換した整数$n' = n - (10a + b) + (10b + a)$を考える。
$n' - n = 9(b - a)$であるが、目的の公約数$d$は$d \mid n, n'$であるので$d \mid 9(b-a)$が言える。

末尾に限らない位置でこれを行うと差は$9(b-a) \cdot 10^k$。
置換は互換の積であるので、この隣合う$2$数字の交換だけでできる整数を考えればよい。
よって、数字$a,b$が$n$に含まれていることを$\phi(a,b)$として、目的の$d$は$\forall a b. \phi(a, b) \to d \mid 9(b-a)$を満たす最大の整数となる。

### 乱択解

`2222222222222222222222222221222222222222222222222222222222`みたいなので落ちる。
$\mathrm{gcd}$に約数として$2$が含まれないが、これを確認するには`1`が末尾に来る必要がある。

## implementation

### 想定解

``` python
#!/usr/bin/env python3
import math
n = input()
cnt = [ n.count(c) for c in '0123456789' ]
d = int(n)
for a in range(10):
    for b in range(a+1, 10):
        if cnt[a] and cnt[b]:
            d = math.gcd(d, (10*b+a)-(10*a+b))
print(d)
```

### 乱択解 (AC $\to$ WA)

``` python
#!/usr/bin/env python2
import random
import fractions
n = list(raw_input())
d = int(''.join(n))
for i in range(200):
    random.shuffle(n)
    x = int(''.join(n))
    d = fractions.gcd(d, x)
print(d)
```
