---
layout: post
redirect_from:
  - /writeup/algo/codeforces/713-b/
  - /blog/2016/09/14/cf-713-b/
date: "2016-09-14T12:42:36+09:00"
tags: [ "competitive", "writeup", "codeforces", "cf", "reactive", "binary-search" ]
"target_url": [ "http://codeforces.com/contest/713/problem/B" ]
---

# Codeforces Round #371 (Div. 1) B. Searching Rectangles

実装に$1$時間ぐらいかかってしまったので提出見送りにした。
しかもそのままではWAだったらしい。危なかった。

## solution

Use binary search, $3$ times.
The first time is to get the AABB of $A \cup B$, the rest are for to determine the $A$ and $B$.

## implementation

`pwntools` is useful.

``` python
#!/usr/bin/env python3
import sys
def contain(a, b):
    ax1, ay1, ax2, ay2 = a
    bx1, by1, bx2, by2 = b
    return bx1 <= ax1 and ax2 <= bx2 and by1 <= ay1 and ay2 <= by2
def ask(x1, y1, x2, y2, known=(), memo={}):
    if x2 < x1+1 or y2 < y1+1:
        return 0
    ofs = len(list(filter(lambda rect: contain(rect, (x1, y1, x2, y2)), known)))
    key = (x1+1, y1+1, x2, y2)
    if key in memo:
        return memo[key] - ofs
    print('?', *key)
    sys.stdout.flush()
    memo[key] = int(input())
    return memo[key] - ofs
def binsearch(l, r, p): # (l,r], return the smallest n which p holds
    assert l < r
    while l+1 != r:
        m = (l + r) // 2
        if p(m):
            r = m
        else:
            l = m
    return r
def shrink(x1, y1, x2, y2, cnt, known=()):
    assert ask(x1, y1, x2, y2, known=known) == cnt
    x1 = binsearch(x1, x2, lambda x: ask(x, y1, x2, y2, known=known) != cnt) - 1
    y1 = binsearch(y1, y2, lambda y: ask(x1, y, x2, y2, known=known) != cnt) - 1
    x2 = binsearch(x1, x2, lambda x: ask(x1, y1, x, y2, known=known) == cnt)
    y2 = binsearch(y1, y2, lambda y: ask(x1, y1, x2, y, known=known) == cnt)
    assert ask(x1, y1, x2, y2, known=known) == cnt
    assert ask(x1, y1, x2, y2, known=known) == cnt
    return x1, y1, x2, y2
def go(x1, y1, x2, y2):
    assert ask(x1, y1, x2, y2) == 2
    x1, y1, x2, y2 = shrink(x1, y1, x2, y2, 2)
    a = None
    if not a and x1 < x2:
        if ask(x1+1, y1, x2, y2) == 1:
            a = shrink(x1+1, y1, x2, y2, 1)
        elif ask(x1, y1, x2-1, y2) == 1:
            a = shrink(x1, y1, x2-1, y2, 1)
    if not a and y1 < y2:
        if ask(x1, y1+1, x2, y2) == 1:
            a = shrink(x1, y1+1, x2, y2, 1)
        elif ask(x1, y1, x2, y2-1) == 1:
            a = shrink(x1, y1, x2, y2-1, 1)
    if not a:
        a = x1, y1, x2, y2
        return a, a
    else:
        b = shrink(x1, y1, x2, y2, 1, known=[ a ])
        return a, b
n = int(input())
a, b = go(0, 0, n, n)
ax1, ay1, ax2, ay2 = a
bx1, by1, bx2, by2 = b
print('!', ax1+1, ay1+1, ax2, ay2, bx1+1, by1+1, bx2, by2)
```

``` python
#!/usr/bin/env python2
import ast
import random
import sys
from pwn import * # https://pypi.python.org/pypi/pwntools
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('program')
parser.add_argument('-n', default=65536, type=int)
parser.add_argument('-a')
parser.add_argument('-b')
args = parser.parse_args()
context.log_level = 'debug'

n = args.n
log.info('N: %d', n)
if args.a:
    ax1, ay1, ax2, ay2 = ast.literal_eval(args.a)
else:
    ax1 = random.randint(1, n)
    ay1 = random.randint(1, n)
    ax2 = random.randint(1, n)
    ay2 = random.randint(1, n)
    ax1, ax2 = sorted([ ax1, ax2 ])
    ay1, ay2 = sorted([ ay1, ay2 ])
a = ( ax1, ay1, ax2, ay2 )
log.info('A: %d %d %d %d', *a)
if args.b:
    bx1, by1, bx2, by2 = ast.literal_eval(args.b)
else:
    bx1 = random.randint(1, n)
    by1 = random.randint(1, n)
    bx2 = random.randint(1, n)
    by2 = random.randint(1, n)
    bx1, bx2 = sorted([ bx1, bx2 ])
    by1, by2 = sorted([ by1, by2 ])
b = ( bx1, by1, bx2, by2 )
log.info('B: %d %d %d %d', *b)

p = process(args.program, stderr=sys.stderr)
p.sendline(str(n))

for i in range(200):
    line = p.recvline()
    c = line.split()[0]
    args = map(int, line.split()[1:])
    assert c in '?!'
    if c == '?':
        assert len(args) == 4
        x1, y1, x2, y2 = args
        assert 1 <= x1 <= x2 <= n
        assert 1 <= y1 <= y2 <= n
        cnt = 0
        if x1 <= ax1 <= ax2 <= x2 and y1 <= ay1 <= ay2 <= y2:
            cnt += 1
        if x1 <= bx1 <= bx2 <= x2 and y1 <= by1 <= by2 <= y2:
            cnt += 1
        p.sendline(str(cnt))
    elif c == '!':
        assert len(args) == 8
        p = tuple(args[:4])
        q = tuple(args[4:])
        if (p == a and q == b) or (p == b and q == a):
            log.info('used queries: %d', i+1)
            log.info('Accepted')
            sys.exit(0)
        else:
            log.info('Wrong Answer')
            sys.exit(1)
log.info('Query Limit Exceeded')
sys.exit(1)
```
