---
layout: post
redirect_from:
  - /writeup/golf/yukicoder/560/
  - /blog/2017/08/26/yuki-560/
date: "2017-08-26T00:14:14+09:00"
tags: [ "competitive", "writeup", "yukicoder", "golf", "dc" ]
"target_url": [ "https://yukicoder.me/problems/no/560" ]
---

# Yukicoder No.560 ふしぎなナップサック

## solution

$M \gets \frac{2M}{3} + \frac{M + 1}{3} + \frac{0}{3}$をするのは$\frac{1}{3}$増やすのと同じなので$M + \frac{N}{3}$。$O(1)$。

## implementation

``` sh
dc -e9k?3/+p
```

`$0+=$2/3`を考えたがawkは対応してないし出力精度も足りなかった。
