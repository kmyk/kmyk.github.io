---
layout: post
alias: "/blog/2018/03/08/abc-089-a/"
title: "AtCoder Beginner Contest 089: A - Grouping 2"
date: "2018-03-08T12:03:38+09:00"
tags: [ "competitive", "writeup", "atcoder", "abc", "golf" ]
"target_url": [ "https://beta.atcoder.jp/contests/abc089/tasks/abc089_a" ]
---

## implementation

perl $11$byte

``` perl
print<>/3|0
```

次のようなbrainfuck解も書いたけどcellが$8$bitなのでoverflowを避けきれずWA: <https://beta.atcoder.jp/contests/abc089/submissions/2154948>。
せめて$N \le 600$とかだったら答えだけでも$1$cellに収まってなんとかできるのだが。