---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/517/
  - /blog/2017/05/28/yuki-517/
date: "2017-05-28T23:58:50+09:00"
tags: [ "competitive", "writeup", "yukicoder", "greedy" ]
"target_url": [ "http://yukicoder.me/problems/no/517" ]
---

# Yukicoder No.517 壊れたアクセサリー

星$2$ってやるだけ面倒みたいな問題が多くてできればパスしたい。

## solution

貪欲。文字種$L \le 26$に対し$O(L)$。

$A\_i$と$B\_j$で先頭の文字が一致するものを探す。ちょうどひとつ見付からなければ復元不能。
後は短い方を長い方に従って伸ばすことを繰り返せばよい。

## implementation

``` python
#!/usr/bin/env python3
import string

def find_startswith(c, xs):
    for x in xs:
        if x.startswith(c):
            return x
def find_roots(xs, ys):
    for c in string.ascii_uppercase:
        if find_startswith(c, xs) and find_startswith(c, ys):
            yield c

def solve(xs, ys):
    n = len(''.join(xs))
    roots = list(find_roots(xs, ys))
    if len(roots) != 1:
        return -1
    root, = roots
    a = find_startswith(root, xs)
    b = find_startswith(root, ys)
    while len(a) < n or len(b) < n:
        if len(a) < len(b):
            c = b[len(a) :][0]
            d = find_startswith(c, xs)
            if not d:
                return -1
            a += d
        elif len(b) < len(a):
            c = a[len(b) :][0]
            d = find_startswith(c, ys)
            if not d:
                return -1
            b += d
        else:
            return -1
    assert a == b
    return a

n = int(input())
xs = [ input() for _ in range(n) ]
m = int(input())
ys = [ input() for _ in range(m) ]
print(solve(xs, ys))
```
