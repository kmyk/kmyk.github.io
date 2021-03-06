---
category: blog
layout: post
date: 2014-11-11T17:19:37+09:00
tags: [ "algorithm" ]
math: true
---

# CODE FESTIVAL 2014 あさぷろ Middle C : eject

<https://beta.atcoder.jp/contests/code-festival-2014-morning-middle/tasks/code_festival_morning_med_c>

誤差死して悔しかったのでまとめておく

<!-- more -->

## 概要
eject工作をしてスイッチを押す機械を作ったが、1回の開閉につき確率`p`でしかスイッチを押せない  
`n`回開閉した結果、初期状態がOFFのスイッチがONになっている確率を求める

### 制約
-   $ n \le 10^{18} $
-   $ 0 \le p \le 1 $ 小数第10位まで与えられる
-   出力 絶対/相対誤差のうち少なくとも片方が$ 10^{−6} $以下

## $O(\log{N})$ 誤差死解法 doubling
$2^k$回開閉した結果を計算したのち、`n`の2進展開に基づき計算  
`__float128`(GCC拡張)で計算すれば通る

これしか思いつけず、かつ`__float128`どころか`long double`も知らなかったのでだめだった

## $O(\log{N})$ 解法 doubling + 正規化
任意の時点で、ONである確率`on`とOFFである確率`off`は`on + off = 1`を満たす
よって、(`off`を`1 - on`という形でなく別個に求めて)

``` haskell
on'  = on  / (on + off)
off' = off / (on + off)
```

という処理を挟めば精度が向上する  
`double`でもACできる

## $O(1)$ 解法 数学
二項定理を用いる  
スイッチが奇数回押されればONであるので、

$$
    \mathrm{on}  = \sum_{i=1,3,\cdots}^n {}_n\mathrm{C}_i p^i (1-p)^{n-i} \\
    \mathrm{off} = \sum_{i=0,2,\cdots}^n {}_n\mathrm{C}_i p^i (1-p)^{n-i}
$$

すると偶奇性より

$$
    (+ p + (1 - p))^n = + \mathrm{on} + \mathrm{off} \\
    (- p + (1 - p))^n = - \mathrm{on} + \mathrm{off}
$$

変形し

$$
    (1 - 2p)^n = 1 - 2on \\
    \mathrm{on} = \frac{1 - (1 - 2p)^n}{2}
$$

`cmath`の`pow`は魔法のようなアルゴリズムにより$O(1)$らしく、1行で求まってしまう  
`long double`でACできる (`double`はWAした)
