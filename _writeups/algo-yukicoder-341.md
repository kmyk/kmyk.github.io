---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/341/
  - /blog/2016/05/28/yuki-341/
date: 2016-05-28T09:12:07+09:00
tags: [ "competitive", "writeup", "yukicoder", "regex" ]
"target_url": [ "http://yukicoder.me/problems/563" ]
---

# Yukicoder No.341 沈黙の期間

やるだけだけど嫌いではない

``` python
#!/usr/bin/env python3
import re
print(max([len(w) for w in re.split('[^…]', input())]))
```
