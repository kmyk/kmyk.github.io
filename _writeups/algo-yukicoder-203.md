---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/203/
  - /blog/2016/08/16/yuki-203/
date: "2016-08-16T22:25:34+09:00"
tags: [ "competitive", "writeup", "yukicoder", "sed" ]
"target_url": [ "http://yukicoder.me/problems/no/203" ]
---

# Yukicoder No.203 ゴールデン・ウィーク(1)

まだ縮むはず。具体的には`s/^/0/`を`s/x.*//`に吸わせられる気がしてる。

``` sed
#!/bin/sed -f
N
s/\n//
:
s/^x//
s/\(^\|x\)\(o*\)x\+\2o/x\2o/
t
s/x.*//
s/^/0/
:1
s/o/<<123456789o01>/
s/\(.\)<.*\1\(o*.\).*>/\2/
t1
```
