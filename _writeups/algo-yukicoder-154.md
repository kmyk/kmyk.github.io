---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/154/
  - /blog/2016/10/22/yuki-154/
date: "2016-10-22T12:08:53+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/no/154" ]
---

# Yukicoder No.154 市バス

愚直に確認するだけでよい。
以前twitterでこの問題が人気だったことがあった気がするが、なぜ。

``` python
#!/usr/bin/env python3
def is_possible(s):
    w0 = 0
    w1 = False
    g = 0
    r = 0
    for c in s:
        if c == 'W':
            w0 += 1
            w1 = True
        elif c == 'G':
            if not w0:
                return False
            w1 = False
            w0 -= 1
            g  += 1
        elif c == 'R':
            if not g:
                return False
            g -= 1
            r += 1
    if w1 or g:
        return False
    return True

t = int(input())
for _ in range(t):
    s = input()
    print('possible' if is_possible(s) else 'impossible')
```
