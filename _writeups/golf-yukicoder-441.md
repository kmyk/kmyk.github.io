---
layout: post
redirect_from:
  - /writeup/golf/yukicoder/441/
  - /blog/2016/11/12/yuki-441/
date: "2016-11-12T19:56:18+09:00"
tags: [ "competitive", "writeup", "yukicoder", "golf", "bash", "dc" ]
"target_url": [ "http://yukicoder.me/problems/no/441" ]
---

# Yukicoder No.441 和か積

(tailsさんのperl $37$byte解をカンニングして)dc $40$byteだった。

``` sh
dc -e[S][P][[E]1]se?1-r1-*d1=e[r]sr1\>rp
```
