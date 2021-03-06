---
layout: post
redirect_from:
  - /writeup/algo/codeforces/346-a/
  - /blog/2015/10/31/cf-346-a/
date: 2015-10-31T13:58:54+09:00
tags: [ "codeforces", "competitive", "writeup", "gcd", "euclidean-algorithm" ]
---

# Codeforces Round #201 (Div. 1) A. Alice and Bob

部内練習会。ししょーが5分で通してた。私は、まじかよって言いながら1時間かけて通した。
証明せずにとりあえず投げてみたら通った。

<!-- more -->

## [A. Alice and Bob](http://codeforces.com/contest/346/problem/A) {#a}

### 問題

正整数の集合が与えられる。ふたりのプレイヤーがいる。
集合から異なる2数を取ってきて、その差の絶対値を集合に加える(既に入っている場合はだめ)という操作を交互に繰り返す。
この操作を行なえなかったプレイヤーの負けである。最適に動いた場合の勝者を答えよ。

### 解法

どのような順で数を加えても結果は変わらないため、最終的にできる集合の大きさを答える問題と読み替えてよい。

初期の集合$X$の最大の数を$b$, 結果の集合$Y$に含まれる最小の数を$a$とすると$a = \gcd X$であり、結果の集合は$\\{ a, 2a, 3a, \dots, b \\}$である。

初期の集合$X$の各要素を$\gcd X$で割った集合$X'$の要素$x, y \in X'$を考える。
この$x, y$から始めてeuclid互除法を行なうと、$\gcd\\{x, y\\} = 1$であることから、$1$が作れることが分かる。
集合中に$1$が存在すれば、集合中の最大の数以下の全ての数を作ることができる。
よって、この結果を$X$に戻せば、結果の集合$Y$の最小の数$a = \gcd X$であり、$Y = \\{ a, 2a, 3a, \dots, b \\}$である。

### 実装

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat(i,n) repeat_from(i,0,n)
typedef long long ll;
using namespace std;
ll gcd(ll a, ll b) {
    if (b <= a) swap(a,b);
    while (a) {
        ll a0 = a;
        a = b % a0;
        b = a0;
    }
    return b;
}
int main() {
    int n; cin >> n;
    vector<ll> a(n); repeat (i,n) cin >> a[i];
    ll d = a[0];
    repeat (i,n) d = gcd(d, a[i]);
    repeat (i,n) a[i] /= d;
    cout << (((*max_element(a.begin(), a.end()) - n) % 2) ? "Alice" : "Bob") << endl;
    return 0;
}
```
