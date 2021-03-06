---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/557/
  - /blog/2017/10/03/yuki-557/
date: "2017-10-03T03:56:27+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "https://yukicoder.me/problems/no/557" ]
---

# Yukicoder No.557 点対称

## solution

$O(\log N)$。

中央に(もし存在すれば)来られるのは$1, 8$のみ。
それ以外の場所は$1, 6, 8, 9$のどれかで、上半分を決めれば下半分は一意に定まる。
ただし先頭に$0$はだめ。
上半分だけ見るとして各桁は独立なので掛け算すれば答え。

## implementation

``` python
#!/usr/bin/env python3
mod = 10 ** 9 + 7
n = int(input())
result = 1
if n == 1:
    result *= 2 # 1, 8
else:
    if n % 2 == 1:
        result *= 3 # 1, 8, 0
    result *= 4 # 1, 8, 6, 9
    result *= pow(5, n // 2 - 1, mod) # 1, 8, 0, 6, 9
print(result % mod)
```
