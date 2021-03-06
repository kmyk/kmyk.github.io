---
redirect_from:
  - /writeup/algo/atcoder/agc027_d/
layout: post
date: 2019-04-19T23:59:59+09:00
tags: ["competitive", "writeup"]
---

# AtCoder Grand Contest 027: D - Modulo Matrix

## 問題

主に次を満たすように $N \times N$ のマス目上に整数を配置せよ: 

-   ある $m$ が存在し、$4$ 近傍で隣接する整数 $x, y$ をどう取り出しても $\max(x, y) \equiv m \pmod{\min(x, y)}$ を満たす。

## 考察過程

1.  入力が $N \le 500$ のみと少ないので埋め込みが可能
1.  $N = 500$ で解ければ、他は単にその結果から抜き出すだけでよい。
1.  とりあえず $1$ 行のみで列とみなせる場合を考えたい。
    -   $m + 1, 2m + 1, 3m + 1, 4m + 1, \dots$ という系列でできる。
1.  $i \lt i'$ かつ $j \lt j'$ のとき $a _ {i, j} \lt a _ {i', j'}$  を仮定するとどうか。
    -   まず小さな場合として $$ \left( \begin{matrix}
            a & b \\
            c & d \\
        \end{matrix} \right) $$ と並べる。このとき
        $$ \begin{array}{c}
            d = k_1 c + m \\
            d = k_2 b + m \\
            c = k_3 a + m \\
            b = k_4 a + m \\
        \end{array} $$ が必要である。これは
        $$ \begin{array}{rcl}
            d & = & k_1 k_3 a + (k_1 + 1) m \\
            & = & k_2 k_4 a + (k_2 + 1) m \\
        \end{array} $$ が必要である。
1.  ななめに切って偶数行目と奇数行目で谷と山になるように配置するのはどうか。特に谷の行の LCM を考えるといい感じにならないか。
1.  盤面を白黒の市松模様に塗る。まず白マスにランダムに数を配置。次に黒マスをその周囲のマスの LCM + m に設定する。かなりよさそう。
1.  LCM だと数が大きくなりやすい。逆にして、黒マス - m の GCD を白マスに書くのはどうか。
1.  両方をやるとどうか。左上から順に、交互に LCM と GCD を取るように決めていく。
1.  方向はあってると思うが、書いてみてもうまくいかない。時間切れのため諦め。

## 解法

盤面を白黒の市松模様に塗る。
白マスに適切に数を配置すれば、黒マスをその周囲のマスの LCM + m に設定すればよくなる。
そのような白マスは、 $2$ 通りのななめ方向の列に分解しその重ね合わせとみて、それぞれの列に素数を割り当てるとできる。
editorial の図を参照のこと。

素数列挙は済ませてあるとして、 GCD の計算が乗って $O(N^2 \log N)$。
素数列挙は $\pi^{-1}(y) = y - \log y$ なので大きさ $O(\pi^{-1}(N)) = O(N)$ までの素数を列挙すればよく、これは Eratosthenes の篩で $O(N \log \log N)$。

## 反省

-   重ね合わせへの分解の発想がでなかった
-   問題を帰着させたのだから、それより手前の部分を忘れて本質部分を抜き出した形にきちんと整理すべきだった
-   $4$ 近傍での接続関係は即 $2$ 部グラフを思い出すべきだった。行列という言葉に惑わされない

## リンク

-   <https://atcoder.jp/contests/agc027/tasks/agc027_d>
-   <https://not-522.appspot.com/contest/6460053427060736>
-   <https://atcoder.jp/contests/agc027/submissions/5021348>
