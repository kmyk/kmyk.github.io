---
layout: post
redirect_from:
  - /writeup/golf/yukicoder/558/
  - /blog/2017/08/25/yuki-558/
date: "2017-08-25T23:47:03+09:00"
tags: [ "competitive", "writeup", "yukicoder", "golf", "brainfuck", "sed" ]
"target_url": [ "https://yukicoder.me/problems/no/558" ]
---

# Yukicoder No.558 575検出するやつ

`unko`

## solution

嘘解法。
隣接する$3$文字$a, b, c$で$3a - 2b + c = 102$なものがあるか探す。
$O(N)$。
$(3, -2, 1)$でなく$(256^2, 256^1, 256^0)$だと嘘でないが長くなる。

## implementation

### brainfuck (cheat)

圧縮して$117$byte。
`^@`はnull文字。末尾に改行なし。単に書いただけなのでまだまだ縮むはずです。

``` c++
#!/usr/bin/env bfi
+[-<+]
+[->>,+]
<<[[>>+>>-->>+++<<<<<<-]<<]
>>>+>[>+>]<[<]<<
<[>>>>[>->]<<[<<]<<-]
>+>+>+[>]
>[<]
<[<]
<[<]
<[<]
<[.<]
^@SEY^@ON^@f
```

### sed

Bashにくるむ必要のため$19$byte。

``` sh
#!/bin/sh
sed '/575/cYES
cNO'
```
