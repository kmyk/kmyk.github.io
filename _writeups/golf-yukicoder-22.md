---
layout: post
redirect_from:
  - /writeup/golf/yukicoder/22/
  - /blog/2017/04/11/yuki-22/
date: "2017-04-11T21:52:32+09:00"
tags: [ "competitive", "writeup", "yukicoder", "golf", "vim-script" ]
"target_url": [ "http://yukicoder.me/problems/no/22" ]
---

# Yukicoder No.22 括弧の対応

vimの`%`にまかせて($2$年前の)tailsさんによるperl $60$byteを抜いて暫定最短。

## implementation

vim (`vim -u NONE -i NONE -X -N -n -e -s -S $FILENAME /dev/stdin -c qa!`) $54$byte:

``` vim
norm Dppk0cwnorm^j
exe getline(1)."lhh%"
pu=col('.')
p
```
