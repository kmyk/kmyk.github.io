---
layout: post
redirect_from:
  - /writeup/golf/yukicoder/410/
  - /blog/2016/10/06/yuki-410/
date: "2016-10-06T05:18:30+09:00"
tags: [ "competitive", "writeup", "yukicoder", "golf" ]
"target_url": [ "http://yukicoder.me/problems/no/410" ]
---

# Yukicoder No.410 出会い

tailsさんがdcで無双していた。

perlだけrubyだけで見ても$1$位ならず。`-p0a`からの`$F`によるparseがよいようだ。

``` perl
print abs(($_=<>.($t=<>))-$t)/2+abs(~/ .* /s&&$&-$')/2
```

``` ruby
c=0;gets.split.zip(gets.split){|a,b|c+=(a.to_i-b.to_i).abs};p c/2.0
```
