---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/292/
  - /blog/2016/06/11/yuki-292/
date: 2016-06-11T18:47:18+09:00
tags: [ "competitive", "writeup", "yukicoder", "sed" ]
"target_url": [ "http://yukicoder.me/problems/725" ]
---

# Yukicoder No.292 芸名

sedはgotoもifも組み込みで存在していて楽ですね。

``` sed
#!/bin/sed -f

# make unary numbers
:
s/[1-9]/&-/g
y/123456789/012345678/
t
s/-0/9-/
t
s/00/0/g

# iterate chars
s/^/;/
s/$/ /
:1
/0[^-]/{
    s/;./; /
    s/0[^-]//g
}
s/;\(.\)/\1;/
s/0-/0/g
s/; .*//
t1
s/ //g
```
