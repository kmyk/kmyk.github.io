---
redirect_from:
  - /writeup/golf/yukicoder/713/
layout: post
date: 2018-07-13T23:30:28+09:00
tags: [ "competitive", "writeup", "yukicoder", "golf", "perl" ]
"target_url": [ "https://yukicoder.me/problems/no/713" ]
---

# yukicoder No.713 素数の和

## 鑑賞

### perl 40byte

perl 40byte, %20さんによる: <https://yukicoder.me/submissions/272634>

``` perl
$\+=$_*`factor $_`=~/ $_/for 1..<>;print
```

`factor` を使う案は私が出したものだが、出力行区切り`$\`に貯めて無引数`print`というおそらく典型テクを知らなかったのでやられた

### perl 44byte

perl 44byte (no execve), %20さんによる: <https://yukicoder.me/submissions/272425>

``` perl
map$%+=1x$_~~/^(11+)\1+$/?0:$_,2..<>;print$%
```

長さ$n$の文字列`111...1`対し`/^(11+)\1+$/`で合成数判定をしている。
上と同様の`$\`の技法は$0$の周りでWAになる。
