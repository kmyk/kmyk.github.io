---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/394/
  - /blog/2016/07/16/yuki-394/
date: "2016-07-16T00:02:19+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/no/394" ]
---

# Yukicoder No.394 ハーフパイプ(1)

sortして真ん中$4$つの平均を取ればよい。
やるだけだけど、後の問題の布石になってるのは良いですね

``` python
#!/usr/bin/env python3
print('%.2f'%(sum(sorted(map(int,input().split()))[1:5])/4))
```
