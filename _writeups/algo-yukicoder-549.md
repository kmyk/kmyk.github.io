---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/549/
  - /blog/2017/07/29/yuki-549/
date: "2017-07-29T00:10:12+09:00"
tags: [ "competitive", "writeup", "yukicoder", "greedy" ]
"target_url": [ "https://yukicoder.me/problems/no/549" ]
---

# Yukicoder No.549 素材合成システム

絶対にコーナーケース埋まってるでしょと思いながらかかったが何もなかった。意外だ。

## solution

最大のやつに他を全部直接合成すればよい。$O(N)$でできる。

## implementation

$O(N \log N)$。

``` python
#!/usr/bin/env python3
n = int(input())
xs = sorted(map(int, input().split()))
xs, y = xs[: -1], xs[-1]
for x in xs:
    y += x // 2
print(y)
```
