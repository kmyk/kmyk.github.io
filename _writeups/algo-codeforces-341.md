---
layout: post
redirect_from:
  - /writeup/algo/codeforces/341/
  - /blog/2015/09/24/cf-341/
date: 2015-09-24T21:37:51+09:00
tags: [ "codeforces", "competitive", "writeup", "longest-increasing-subsequence", "lis" ]
---

# Codeforces Round #198 (Div. 1)

茶会。2完。
参加者が皆揃ってAで苦戦してしまった。なぜなのか


<!-- more -->

## [A. Tourist Problem](http://codeforces.com/contest/341/problem/A) {#a}

特に難しさはなかったはずだが50分3WA。20分ぐらいで一発ACしたかった。

-   `accumulate(a.begin(), a.end(), 0)`ってやってoverflowさせてWA (`0ll`とするのが正しい)
-   $n \le 10^5$なので$O(n^2)$では通らない、というのを見てなくてWA。

### 問題

正数の列が与えられ、その列を適当に並べ替え先頭に0を加えたときの、隣接項間の絶対値の総和の期待値を有理数で求める。

### 解法

まず、

-   入力された列は適当に並び替えてよい。
-   数$a$と$b$が隣接する回数と、数$c$と$d$が隣接する回数は同じ。
-   どの数も先頭に来る回数は同じ。

ここからもう少し考えれば、$\Sigma_i a_i$と$\Sigma_i \Sigma_j \| a_i - a_j \|$の関数であること、さらに、
$\frac{ \Sigma_i a_i + \Sigma_i \Sigma_j \| a_i - a_j \| }{ n }$
であることが分かる。

$n \le 10^5$であるので2重loopは回せないが、事前に整列しておけば線形でなんとかなり、ACが得られる。


### 解答

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat(i,n) repeat_from(i,0,n)
typedef long long ll;
ll gcd(ll a, ll b) {
    if (b <= a) std::swap(a,b);
    while (a) {
        ll a0 = a;
        a = b % a0;
        b = a0;
    }
    return b;
}
using namespace std;
int main() {
    int n; cin >> n;
    vector<ll> a(n); repeat (i,n) cin >> a[i];
    sort(a.begin(), a.end());
    vector<ll> acc(n+1); repeat (i,n) acc[i+1] = acc[i] + a[i];
    ll x = 0;
    repeat (i,n) {
        x += a[i];
        x += acc[n] - acc[i] - (n - i) * a[i];
        x += - acc[i] + i * a[i];
    }
    ll z = gcd(x, n);
    cout << x/z << " " << n/z << endl;
    return 0;
}
```

テスト/実験用

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat(i,n) repeat_from(i,0,n)
typedef long long ll;
ll gcd(ll a, ll b) {
    if (b <= a) std::swap(a,b);
    while (a) {
        ll a0 = a;
        a = b % a0;
        b = a0;
    }
    return b;
}
using namespace std;
int main() {
    int n; cin >> n;
    vector<ll> a(n); repeat (i,n) cin >> a[i];
    sort(a.begin(), a.end());
    ll x = 0, y = 0;
    do {
        x += a[0];
        repeat_from (i,1,n) x += abs(a[i] - a[i-1]);
        y += 1;
    } while (next_permutation(a.begin(), a.end()));
    ll z = gcd(x, y);
    cout << x/z << " " << y/z << endl;
    return 0;
}
```

overflowのデバッグ用

``` haskell
module Main where

import Control.Applicative

main :: IO ()
main = do
    n <- readLn
    a <- map read . words <$> getLine
    let x = sum ((\ p q -> abs $ p - q) <$> a <*> a) + sum a
    let z = gcd x n :: Integer
    putStrLn . unwords $ map show [x `div` z, n `div` z]
```


## [B. Bubble Sort Graph](http://codeforces.com/contest/341/problem/B) {#b}

### 問題

$1 \dots n$の順列したものが与えられ、それをbubble sortする。
その際、$n$頂点の空グラフを用意し、整列の過程で$a$と$b$をswapしたとき、$a$番目の頂点と$b$番目の頂点とに辺を張る。
こうしてできたグラフの最大独立集合の大きさを求める。

### 解法

まず初手の考察として、

-   bubble sortであるので、与えられた数列で順序が逆転している数の組に辺が張られる。
-   最大独立集合を求める問題は一般にNP困難である。
-   $O(n^2)$では間に合わない。

解法が線形あるいは準線形となることから、前や後ろから貪欲的に集合を作ることを考える。

まずは前から貪欲的に採用していくとする。
ある数$n$を採用した場合、数列中で$n$以降の位置にあって$n$より小さい数を採用できなくなる。しかし反例として`100 1 2 3 4 ... 99`のようなものが存在する。

ここで、できるだけ採用する回数を増やしたいこと、採用する数字は単調に増加することを考えると、これは最長増加部分列であり、これが解法となる。

あるいは`5 6 7 1 2 3 4`や`5 6 7 4 3 2 1`といった例を眺めていれば気が付く。

### 解答

最長増加部分列は準線形時間で求まることは覚えていたが書けなかった。
しかし手元の今まで書いたコードの山をgrepしたらそれっぽいのがでてきたので、それをそのまま投げたら通った。
[最長増加部分列 \| 動的計画法 \| Aizu Online Judge](http://judge.u-aizu.ac.jp/onlinejudge/commentary.jsp?id=DPL_1_D) を書いたときのものだった。

以下はACした後に書きなおしたもの。

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat(i,n) repeat_from(i,0,n)
using namespace std;
// longest increasing subsequence
template <typename T>
vector<T> lis(vector<T> const & xs) {
    vector<T> l; // l[i] is the last element of the increasing subsequence whose length is i+1
    l.push_back(xs.front());
    for (auto && x : xs) {
        auto it = lower_bound(l.begin(), l.end(), x);
        if (it == l.end()) {
            l.push_back(x);
        } else {
            *it = x;
        }
    }
    return l;
}
int main() {
    int n; cin >> n;
    vector<int> a(n); repeat (i,n) cin >> a[i];
    cout << lis(a).size() << endl;
    return 0;
}
```

<hr>

ねむたくて頭回ってなかったのと時間足りないだろうという判断からCは開かず撤退。
