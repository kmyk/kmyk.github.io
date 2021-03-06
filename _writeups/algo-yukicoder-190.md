---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/190/
  - /blog/2016/11/01/yuki-190/
date: "2016-11-01T18:25:32+09:00"
tags: [ "competitive", "writeup", "yukicoder", "greedy", "shakutori-method" ]
"target_url": [ "http://yukicoder.me/problems/no/190" ]
---

# Yukicoder No.190 Dry Wet Moist

いい感じにDry, Wet, Moist全部を共通化したかったがだめだった。

## solution

Dry/Wetに関して、しゃくとり法で貪欲。$O(N)$。
Wetは数列全体に$-1$を掛ければDryを求めるのに帰着される。
Dryのみを考える。
負の数に関して、小さい方から$n$個使うのがよい。ある負の数を使うと決めたとき、それと足して負になる数で最大のものを貪欲に使うべき。これをしゃくとり法でやればよい。

Moistに関して、愚直。$O(N)$。
絶対値が同じで符号が異なるものの数を数える。しゃくとり法でもいいし、`map`の類に入れてもよい。
$0$には注意する必要があって、単に個数を数えて$2$で割る。

## implementation

入力は整列済みとは限らないことに注意。

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <map>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using namespace std;
template <typename T> T input(istream & in) { T a; in >> a; return a; }
int foo(vector<int> const & a) {
    int cnt = 0;
    for (int i = 0, j = a.size() - 1; i < j; ++ i, -- j) {
        while (i < j and a[i] + a[j] >= 0) -- j;
        if (i == j) break;
        ++ cnt;
    }
    return cnt;
}
int bar(vector<int> const & xs) {
    map<int,int> f, g;
    int zero = 0;
    for (int x : xs) {
        if (x > 0) {
            f[x] += 1;
        } else if (x < 0) {
            g[- x] += 1;
        } else {
            zero += 1;
        }
    }
    int cnt = 0;
    for (auto it : f) cnt += min(it.second, g[it.first]);
    cnt += zero/2;
    return cnt;
}
int main() {
    int n; cin >> n;
    vector<int> a(2*n); repeat (i,2*n) cin >> a[i];
    whole(sort, a);
    int dry   = foo(a);
    int moist = bar(a);
    repeat (i,2*n) a[i] *= -1; whole(reverse, a);
    int wet   = foo(a);
    cout << dry << ' ' << wet << ' ' << moist << endl;
    return 0;
}
```
