---
layout: post
alias: "/blog/2016/05/28/abc-038-a/"
title: "AtCoder Beginner Contest 038 A - お茶"
date: 2016-05-28T23:00:01+09:00
tags: [ "competitive", "writeup", "atcoder", "abc", "regex", "sed" ]
"target_url": [ "https://beta.atcoder.jp/contests/abc038/tasks/abc038_a" ]
---

sedは楽しい

``` sed
#!/bin/sed -f
s/.*T$/YES/
t
s/.*/NO/
```