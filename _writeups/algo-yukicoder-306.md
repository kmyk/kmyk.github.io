---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/306/
  - /blog/2016/05/31/yuki-306/
date: 2016-05-31T18:24:30+09:00
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/853" ]
---

# Yukicoder No.306 さいたま2008

小中学校あたりで見たことのある感じのそれだが、思い出せなかったらつらい。
$A,P,B$が実質直線になるのが最短で、つまり点$A$の$y$軸対象な点$A'$と点$B$を結ぶ直線$A',B$と$y$軸との交点が目的の点$P$。
もしだめなら三分探索をすればよい。

``` python
#!/usr/bin/env python3
ax, ay = map(int,input().split())
bx, by = map(int,input().split())
ax *= -1
k = (by - ay) / (bx - ax)
py = ay + k * (- ax)
print('%.8f' % py)
```
