---
layout: post
redirect_from:
  - /writeup/algo/codeforces/697-a/
  - /blog/2016/07/15/cf-697-a/
date: "2016-07-15T04:00:21+09:00"
tags: [ "competitive", "writeup", "codeforces" ]
"target_url": [ "http://codeforces.com/contest/697/problem/A" ]
---

# Codeforces Round #362 (Div. 2) A. Pineapple Incident

## problem

$t,s,x$が与えられる。
時刻$t, t+s, t+s+1, t+2s+1, t+3s+1, t+3s+1, \dots$に犬が鳴くのだが、時刻$x$はそのような犬が鳴く時刻であるか答えよ。

## implementation

$t, (t+s, t+s+1), (t+2s, t+2s+1), \dots$と見たが、$(t, t+s), (t+s+1, t+2s), \dots$としたほうがよかった。

``` python
#!/usr/bin/env python3
t, s, x = map(int,input().split())
ans = False
if x == t:
    ans = True
elif x >= t+s and (x - t) % s == 0:
    ans = True
elif x >= t+s and (x - t) % s == 1:
    ans = True
print(ans and 'YES' or 'NO')
```
