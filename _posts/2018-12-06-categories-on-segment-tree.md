---
category: blog
layout: post
date: "2018-12-06T12:00:00+09:00"
tags: [ "competitive", "segment-tree", "monoid", "category" ]
---

# セグメント木の上に乗る構造はモノイドではなく圏である

## 主張

セグメント木の上に乗る構造は多くの場合[モノイド](https://ja.wikipedia.org/wiki/%E3%83%A2%E3%83%8E%E3%82%A4%E3%83%89)であり、たいていの場合はこの理解で十分である。
しかしモノイドからさらに緩めることもできる。
頂点 $$i$$ に乗った要素 $$a_i$$ と頂点 $$i + 1$$ に乗った要素 $$a _ {i+1}$$ との間には演算 $$a_i \cdot a _ {i+1}$$ が定義されている必要があるが、頂点 $$i + 2$$ に乗った要素 $$a _ {i+2}$$ との間の演算 $$a_i \cdot a _ {i+2}$$ が定義されている必要はないためである。
このように全域性が不要であることに注意すると、セグメント木の上に乗る構造は[圏](https://ja.wikipedia.org/wiki/%E5%9C%8F_(%E6%95%B0%E5%AD%A6))であると言える。
特に $$N$$ 頂点のセグメント木の頂点 $$i$$ には射 $$a_i : d_i \to c_i$$ (ただし $$i \lt N - 1$$ に対し $$c_i = d _ {i+1}$$ を満たす) が乗る。
圏とは要素に型の付いたモノイドであり、モノイドとは対象がひとつだけの圏であるので、これはかなり自然な一般化である。

## 例: 区間拡張

各頂点 $$i$$ に値 $$x_i$$ を乗せ、線形関数による操作 $$x \mapsto ax + b$$ と区間和 $$x_l + x _ {l+1} \dots + x _ {r-1}$$ を行なえる遅延伝播セグメント木を考える。
これは頂点に追加で区間 $$[l_i, r_i)$$ を乗せ、演算の際に区間の和集合を $$[l, r) = [l_1, r_1) \cup [l_2, r_2)$$ (ただし $$r_1 = l_2$$) と取るようにすれば計算できる。

さて区間の演算はモノイドではない。
例えば $$[0, 1) \cup [2, 3)$$ は集合ではあるが区間ではなく、演算に対して閉じていない。
よってこれは圏であると言うべきである。
区間 $$[l, r)$$ は対象 $$l$$ から $$r$$ への射 $$: l \to r$$ に対応する。

このような構造に区間の情報を付加する技法は座標圧縮が絡む場合にも見られる。

## 例: 連鎖行列積

各頂点 $$i$$ に $$H_i \times W_i$$ 行列 $$A_i$$ を乗せ、その区間積 $$A_l A _ {l+1} \dots A _ {r-1}$$ を計算したいとする。
ただし $$i \lt N - 1$$ に対し $$W_i = H _ {i+1}$$ とする。
これはセグメント木で計算可能である。

しかし行列の型の条件により構造は明らかにモノイドではなく、全ての行列の圏 $$\mathbf{Mat}$$ と言うべきである。

## 遅延伝播セグメント木の操作について

操作は[関手](https://ja.wikipedia.org/wiki/%E9%96%A2%E6%89%8B)であると言い直される。
ただし[対象を変化させない](https://ncatlab.org/nlab/show/identity-on-objects+functor)という条件が付く。

## 圏として整理することの嬉しさ

セグメント木に対する理解が進んだという点で嬉しさがある。
言語化がなされないままに知られていた事実を整理したというだけであり、問題を解く上で便利になることはない。
