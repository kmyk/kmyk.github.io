---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/589/
  - /blog/2017/12/08/yuki-589/
date: "2017-12-08T07:45:57+09:00"
tags: [ "competitive", "writeup", "yukicoder", "oeis", "experiment" ]
"target_url": [ "https://yukicoder.me/problems/no/589" ]
---

# Yukicoder No.589 Counting Even

面白いとは思うけど真面目に解く人間いなさそう。

## solution

問題文からOEISあるいはサンプルから実験の雰囲気を感じとり、そのようにする。
<https://oeis.org/A048967>。$O(1)$。


## implementation

``` python
#!/usr/bin/env python3
n = int(input())
print(n + 1 - 2 ** bin(n).count('1'))
```
