---
layout: post
redirect_from:
  - /writeup/golf/yukicoder/218/
  - /blog/2017/01/25/yuki-218/
date: "2017-01-25T19:15:33+09:00"
tags: [ "competitive", "writeup", "yukicoder", "golf", "perl", "dc" ]
"target_url": [ "http://yukicoder.me/problems/no/218" ]
---

# Yukicoder No.218 経験値1.5倍

<blockquote class="twitter-tweet" data-lang="en"><p lang="ja" dir="ltr">No.218 経験値1.5倍<a href="https://t.co/5ydUF9IEn7">https://t.co/5ydUF9IEn7</a><br>kimiyuki さんが tails さんからショートコードを奪取しました！ （42B→40B）</p>&mdash; yukicoder最短コード更新お知らせ (@yukigolfer) <a href="https://twitter.com/yukigolfer/status/824198894179467264">January 25, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

## solution

$\lceil \frac{a}{b} \rceil \ge \frac{2}{3} \cdot \lceil \frac{a}{c} \rceil$かどうかを答えればよい。
$\lceil \frac{a}{b} \rceil = (a+b-1)/b = (a-1)/b + 1$を使って、$(a-1)/b\cdot 3 \gt (a-1)/c\cdot 2$を判定すれば同じ。
 
## implementation

### perl $40$byte

``` perl
$a=<>-1;print$a/<><<1>($a/<>|0)*3?YES:NO
```

`$a/<><<1`は`int($a/<>)*2`や`($a/<>|0)*2`に同じ。

### bash $35$byte

``` sh
dc -e[NO][[YES]]sx?1-d?/2*r?/3*\<xp
```

dcで普通に実装するだけで暫定最短を奪取できた。
