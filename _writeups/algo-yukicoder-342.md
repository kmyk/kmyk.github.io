---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/342/
  - /blog/2016/05/28/yuki-342/
date: 2016-05-28T09:12:10+09:00
tags: [ "competitive", "writeup", "yukicoder", "regex" ]
"target_url": [ "http://yukicoder.me/problems/564" ]
---

# Yukicoder No.342 一番ワロタｗｗ

anagolに出題すべきだった問題

haskellで書いていればもっとすっきりしていたはず

``` python
#!/usr/bin/env python3
import re
from itertools import takewhile
s = input()
ws = re.split('ｗ(?=[^ｗ]|$)', s)
ws = ws[:-1]
ws = list(filter(lambda w: len(w) and w[0] != 'ｗ', ws))
ws = list(map(lambda w: (w + 'ｗ').partition('ｗ'), ws))
ws = list(sorted(ws, key=lambda p: len(p[2]), reverse=True))
ws = list(takewhile(lambda p: len(p[2]) == len(ws[0][2]), ws))
ws = list(map(lambda p: p[0], ws))
list(map(print, ws))
```
