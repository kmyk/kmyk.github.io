---
redirect_from:
  - /writeup/algo/yukicoder/720/
layout: post
date: 2018-07-28T01:42:46+09:00
tags: [ "competitive", "writeup", "yukicoder", "matrix", "exponentiation-by-squaring" ]
"target_url": [ "https://yukicoder.me/problems/no/720" ]
---

# Yukicoder No.720 行列のできるフィボナッチ数列道場 (2)

## note

解法はやるだけだけどわりと好き。
入力は$m \le 30000$なので想定は$O(m \log n)$。
ところでOEISでもよいらしい。

## solution

行列累乗やるだけ。$O(\log n + \log m)$。

関数 $$ \begin{pmatrix}
    F_{i+2} \\ F_{i+1} \\ s
\end{pmatrix} = F \begin{pmatrix}
    F_{i+1} \\ F_i \\ s
\end{pmatrix} $$ と関数 $$ \begin{pmatrix}
    a_1 \\ a_0 \\ s + a_0
\end{pmatrix} = G \begin{pmatrix}
    a_1 \\ a_0 \\ s
\end{pmatrix} $$ はそれぞれ線形。
ここで関数 $$ \begin{pmatrix}
    F_{nm+1} \\ F_{nm} \\ \sum_{i \le n} F_{im}
\end{pmatrix} = (GF^m)^n \begin{pmatrix}
    1 \\ 0 \\ 0
\end{pmatrix} $$ なのでこれを計算すればよい。

## implementation

``` python
#!/usr/bin/env python3
import numpy as np

def powmod(f, n, mod):
    g = np.identity(3, dtype=np.uint64)
    for p in map(int, reversed(bin(n)[2 :])):
        if p:
            g = g * f % mod
        f = f * f % mod
    return g

def solve(n, m):
    # \begin{pmatrix} F_{2i+1} \\ F_{2i} \\ \sum_{j \le i} F_{2j} \end{pmatrix}
    x = np.matrix([ 1, 0, 0 ], dtype=np.uint64).transpose()
    f = np.matrix([
        [ 1, 1, 0 ],
        [ 1, 0, 0 ],
        [ 0, 0, 1 ],
    ], dtype=np.uint64)
    g = np.matrix([
        [ 1, 0, 0 ],
        [ 0, 1, 0 ],
        [ 0, 1, 1 ],
    ], dtype=np.uint64)
    mod = 10 ** 9 + 7
    return (powmod(g * powmod(f, m, mod) % mod, n, mod) * x % mod)[2, 0]

print(solve(*map(int, input().split())))
```
