---
layout: post
redirect_from:
  - /writeup/golf/yukicoder/487/
  - /blog/2017/02/24/yuki-487/
date: "2017-02-24T23:57:28+09:00"
tags: [ "competitive", "writeup", "yukicoder", "golf", "dc" ]
"target_url": [ "https://yukicoder.me/problems/no/487" ]
---

# Yukicoder No.487 2017 Calculation(2017の計算)

させたいことは分かるし悪くはないのだけど、$2017 + (2017 \cdot 2017)^{2017} \approx 1.3 \times 10^4$ではさすがに小さすぎて何の工夫もなく直接計算できてしまう。

## implementation

dc $17$byteで暫定最短:

``` sh
dc -e2017ddd+^+?%p
```

``` python
#!/usr/bin/env python3
print((2017+(2017*2017)**2017) % int(input()))
```
