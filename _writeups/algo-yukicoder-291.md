---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/291/
  - /blog/2016/06/22/yuki-291/
date: 2016-06-22T04:05:56+09:00
tags: [ "competitive", "writeup", "yukicoder", "dp" ]
"target_url": [ "http://yukicoder.me/problems/no/291" ]
---

# Yukicoder No.291 黒い文字列

なんだかいけそうだしで、えいってしたのが想定解だった。

## solution

$\operatorname{dp} : N \times N/5 \times N/5 \times N/5 \times N/5 \to \mathbb{N}$なDPをする。

文字列$S$の長さを$N$とすると、部分列`KUROI`は高々$\frac{N}{5}$個しかできない。
これにより、例えば部分列`KUR`も高々$\frac{N}{5}$個のみを考えればよい。
状態数が$N \times N/5 \times N/5 \times N/5 \times N/5 = 100 \times 20 \times 20 \times 20 \times 20 \approx 1.6 \times 10^7$なので、この上で単純にDPをすれば間に合う。

## implementation

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#define whole(f,x,...) ([&](decltype((x)) y) { return (f)(begin(y), end(y), ## __VA_ARGS__); })(x)
template <class T> bool setmax(T & l, T const & r) { if (not (l < r)) return false; l = r; return true; }
using namespace std;
template <typename T, typename X> auto vectors(T a, X x) { return vector<T>(x, a); }
template <typename T, typename X, typename Y, typename... Zs> auto vectors(T a, X x, Y y, Zs... zs) { auto cont = vectors(a, y, zs...); return vector<decltype(cont)>(x, cont); }

int n;
string s;
vector<vector<vector<vector<vector<int> > > > > memo;
int go(int i, int k, int ku, int kur, int kuro) {
    if (i == n) return 0;
    if (memo[i][k][ku][kur][kuro] != -1) return memo[i][k][ku][kur][kuro];
    bool nk     =          k    + 1 <= n/5;
    bool nku    = k    and ku   + 1 <= n/5;
    bool nkur   = ku   and kur  + 1 <= n/5;
    bool nkuro  = kur  and kuro + 1 <= n/5;
    bool nkuroi = kuro                    ;
    if (s[i] == 'K') return memo[i][k][ku][kur][kuro] = go(i+1, k+nk,  ku,      kur,       kuro);
    if (s[i] == 'U') return memo[i][k][ku][kur][kuro] = go(i+1, k-nku, ku+nku,  kur,       kuro);
    if (s[i] == 'R') return memo[i][k][ku][kur][kuro] = go(i+1, k,     ku-nkur, kur+nkur,  kuro);
    if (s[i] == 'O') return memo[i][k][ku][kur][kuro] = go(i+1, k,     ku,      kur-nkuro, kuro+nkuro);
    if (s[i] == 'I') return memo[i][k][ku][kur][kuro] = go(i+1, k,     ku,      kur,       kuro-nkuroi) + nkuroi;
    int acc = -1;
    if (nk)        setmax(acc, go(i+1, k+nk,  ku,      kur,       kuro));
    if (nku)       setmax(acc, go(i+1, k-nku, ku+nku,  kur,       kuro));
    if (nkur)      setmax(acc, go(i+1, k,     ku-nkur, kur+nkur,  kuro));
    if (nkuro)     setmax(acc, go(i+1, k,     ku,      kur-nkuro, kuro+nkuro));
    if (nkuroi)    setmax(acc, go(i+1, k,     ku,      kur,       kuro-nkuroi) + nkuroi);
    if (acc == -1) setmax(acc, go(i+1, k,     ku,      kur,       kuro));
    return memo[i][k][ku][kur][kuro] = acc;
};
int main() {
    cin >> s;
    s.erase(whole(remove_if, s, [&](char c) { return string("KUROI?").find(c) == string::npos; }), s.end());
    n = s.length();
    memo = vectors(-1, n+1, n/5+1, n/5+1, n/5+1, n/5+1);
    cout << go(0, 0, 0, 0, 0) << endl;
    return 0;
}
```
