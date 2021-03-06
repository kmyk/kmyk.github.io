---
redirect_from:
  - /writeup/algo/atcoder/arc027_4/
layout: post
date: 2019-12-18T23:59:59+09:00
tags: ["competitive", "writeup"]
---

# AtCoder Regular Contest 027: D - ぴょんぴょんトレーニング

## 解法

$h_i \le 10$ があるのでセグ木に $10 \times 10$ の行列を乗せてやるだけ。
計算量は $K = \max h_i = 10$ を変数として $O(K^3 N + K^3 D \log N)$。

注意点として:

-   出題当時はこの解法は通らなかった可能性がある。ただし TL $8$ 秒に対し今回の実装が $1.4$ 秒なのでかなり余裕はあるので通っていた可能性もある。editorial ではなぜか平方分割をし、その上で行列で持つと $O(K^3)$ になるところを vector で持つ感じにして $O(K^2)$ に落とすやつを使っている
-   セグ木の構築を $1$ 点更新を $N$ 回発行する形でやると $O(K^3 N \log N)$ になって TLE する
-   セグ木をそのまま持つと $400$ MB ぐらい使ってしまって MLE する。セグ木に乗せる列を中央で分割し、左側のセグ木と右側のセグ木を順番に計算する感じにするとメモリも半分にできて AC する

## メモ

-   制約の見落しに注意
-   いかにもなタイトルで喫茶店なのにうさぎがいない
-   ところでこれ disjoint sparse table みたいにしたら空間 $O(1)$ にできそう

## リンク

-   <https://atcoder.jp/contests/arc027/tasks/arc027_4>
-   実装: <https://atcoder.jp/contests/arc027/submissions/9009219>
