---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/346/
  - /blog/2016/02/26/yuki-346/
date: 2016-02-26T23:45:09+09:00
tags: [ "competitive", "writeup", "yukicoder" ]
---

# Yukicoder No.346 チワワ数え上げ問題

## [No.346 チワワ数え上げ問題](http://yukicoder.me/problems/987)

後ろから走査すれば一回の走査で済みかつlistも不要だった。

``` python
#!/usr/bin/env python3
s = input()
c, w = [], []
for i in range(len(s)):
    if s[i] == 'c':
        c.append(i)
    elif s[i] == 'w':
        w.append(i)
ans = 0
i, j = 0, 0
while i < len(c):
    while j < len(w) and w[j] < c[i]:
        j += 1
    n = len(w) - j
    ans += n * (n-1) // 2
    i += 1
print(ans)
```
