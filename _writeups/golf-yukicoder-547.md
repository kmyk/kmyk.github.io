---
layout: post
redirect_from:
  - /writeup/golf/yukicoder/547/
  - /blog/2017/07/29/yuki-547/
date: "2017-07-29T00:10:06+09:00"
tags: [ "competitive", "writeup", "yukicoder", "golf" ]
"target_url": [ "https://yukicoder.me/problems/no/547" ]
---

# Yukicoder No.547 未知の言語

## implementation

``` python
#!/usr/bin/env python3
n = int(input())
s = input().split()
t = input().split()
for i in range(n):
    if s[i] != t[i]:
        print(i + 1)
        print(s[i])
        print(t[i])
```

## 鑑賞

%20さんのperl $53$byte (まだ収束してなさそうだけど): <https://yukicoder.me/submissions/192339>


``` perl
<>;$/=$",($\=<>)=~/^$_\s/||print$.-2,"
$_
"for glob<>
```

-   冒頭の`<>`は$1$行目の破棄
-   `for glob<>`が先に来て、$2$行目を読んで単語ごとに
-   `$/=$"`して空白区切りで読むことにして
-   $3$行目から単語を出力区切り`$\`で受け
-   $2$行目の単語`$_`を置換し削除、されなかったら(`$/`に依存する)行番号`$.`と`$_`を出力
-   出力区切りに入っていた$3$行目の単語も出力される

`$.`知らなかった
