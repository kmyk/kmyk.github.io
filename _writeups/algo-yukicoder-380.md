---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/380/
  - /blog/2016/07/07/yuki-380/
date: "2016-07-07T23:22:59+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/no/380" ]
---

# Yukicoder No.380 悪の台本

悪問。特にやる価値はない。同writerの他の問題はたいてい良問なのにどうしたのだろう。

``` python
#!/usr/bin/env python3
import sys
for line in sys.stdin:
    for i in range(4):
        if not line or line[-1].isalnum():
            break
        line = line[:-1]
    if line.startswith('digi '):
        ans = line.lower().endswith('nyo')
    elif line.startswith('petit '):
        ans = line.lower().endswith('nyu')
    elif line.startswith('rabi '):
        ans = any(map(lambda c: c.isalnum(), line[5:]))
    elif line.startswith('gema '):
        ans = line.lower().endswith('gema')
    elif line.startswith('piyo '):
        ans = line.lower().endswith('pyo')
    else:
        ans = False
    print(ans and 'CORRECT (maybe)' or 'WRONG!')
```
