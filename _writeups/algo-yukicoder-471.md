---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/471/
  - /blog/2016/12/25/yuki-471/
date: "2016-12-25T02:42:12+09:00"
tags: [ "competitive", "writeup", "yukicoder", "reactive", "matrix" ]
"target_url": [ "http://yukicoder.me/problems/no/471" ]
---

# Yukicoder No.471 直列回転機

CTFのcrypto問でよく見る感じがする。
解法はすぐだったが、名前衝突させて`TypeError`と入力形式のミスで$2$WA生やした。

## solution

平行移動 + 回転 + 平行移動 を繰り返すので、全体としてAffine変換。
以下のような形なので、$3$回クエリを投げれば$6$本の方程式が得られ変換が特定できる。

$$
\begin{pmatrix}
x' \\\\
y' \\\\
1 \\\\
\end{pmatrix} = \begin{pmatrix}
a & b & e \\\\
c & d & f \\\\
0 & 0 & 1 \\\\
\end{pmatrix} \begin{pmatrix}
x \\\\
y \\\\
1 \\\\
\end{pmatrix}
$$

## implementation

``` python
#!/usr/bin/env python3
import sys

m = int(input())
x = [ None ] * m
y = [ None ] * m
for i in range(m):
    x[i], y[i] = map(int, input().split())

def ask(x, y):
    print('?', x, y)
    sys.stdout.flush()
    fx, fy = map(int, input().split())
    return fx, fy

# [ x' ]   [ a b e ] [ x ]
# [ y' ] = [ c d f ] [ y ]
# [ 1  ]   [ 0 0 1 ] [ 1 ]
ae, cf = ask(1, 0)
aae, ccf = ask(2, 0)
a = aae - ae
e = ae - a
c = ccf - cf
f = cf - c
be, df = ask(0, 1)
b = be - e
d = df - f

def rotate(x, y):
    fx = a*x + b*y + e
    fy = c*x + d*y + f
    return fx, fy

print('!')
for i in range(m):
    print(*rotate(x[i], y[i]))
```

