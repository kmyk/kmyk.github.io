---
layout: post
redirect_from:
  - /writeup/golf/yukicoder/46/
  - /blog/2017/01/22/yuki-46/
date: "2017-01-22T20:38:43+09:00"
tags: [ "competitive", "writeup", "yukicoder", "golf", "dc" ]
"target_url": [ "http://yukicoder.me/problems/no/46" ]
---

# Yukicoder No.46 はじめのn歩

暫定最短$13$byteに対し$17$byteであった。

## solution

切り上げて$\mathrm{ans} = \lceil \frac{b}{a} \rceil = \frac{b + (a - 1)}{a}$。
あるいは$\frac{a}{a} = 1$を外に出して$\mathrm{ans} = \frac{b - 1}{a} + 1$。

## implementation

bash $17$byte

``` sh
dc -e?rdsa+1-la/p
```
