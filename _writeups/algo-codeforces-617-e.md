---
layout: post
redirect_from:
  - /writeup/algo/codeforces/617-e/
  - /blog/2017/01/17/cf-617-e/
date: "2017-01-17T18:37:53+09:00"
tags: [ "competitive", "writeup", "codeforces", "mo-algorithm" ]
"target_url": [ "http://codeforces.com/contest/617/problem/E" ]
---

# Codeforces Round #340 (Div. 2): E. XOR and Favorite Number

Mo's algorithmの練習。方針はすぐだったが細部がつらかった。

## problem

数列$a_i$が与えられる。
クエリとして与えられる区間$[l_i, r_i)$ごとに、その中に含まれるxor総和が$k$となるような部分区間の数を答えよ。

## solution

Mo's algorithm。$O((N+M)\sqrt{N})$で空間$O(\max \\{ k, \max a_i \\})$。

事前に累積和$b_i$を取っておくと、区間$[l_i, r_i)$に対する答えは$l_i \le i \lt j \le r_i$で$b_i \oplus b_j \oplus k = 0$なものの数になる。
ある区間$[l, r)$に対する答えと区間中の$b_i$の出現頻度の表があれば、区間$[l \pm 1, r)$や$[l, r \pm 1)$に対してのそれを$O(1)$で計算できる。
$b_i$の出現頻度の表は配列で持ち、答えの増減は丁寧にやる。
これはMo's algorithmを適用でき$O((N+M)\sqrt{N})$。

詳細について。
累積和$b\_i = \sum\_{j \lt i}^{\oplus} a\_j$。
区間$[l,r)$ごとに、答え$\mathrm{ans}\_{l,r} = \|\\{ [i,j) \subseteq [l,r) \mid i \ne j \land b_j \oplus b_i = k \\}\|$、頻度表$c\_{l,r}(i) = \|\\{ j \in [l,r) \mid b_j = i \\}\|$を持つ。
例えば右端を伸ばすとして、$\mathrm{ans}\_{l,r+1} = \|\\{ [i,j) \subseteq [l,r+1) \mid i \ne j \land b\_j \oplus b\_i = k \\}\| = \mathrm{ans}\_{l,r} + \|\\{ i \in [l,r+1) \mid b\_{r+1} \oplus b\_i = k \\}\| = \mathrm{ans}\_{l,r} + c\_{l,r+1}(b\_{r+1} \oplus k)$。

## implementation

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>
#include <cmath>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using ll = long long;
using namespace std;
int main() {
    // input
    int n, m, k; cin >> n >> m >> k;
    vector<int> a(n); repeat (i,n) cin >> a[i];
    vector<int> l(m), r(m); repeat (i,m) { cin >> l[i] >> r[i]; -- l[i]; }
    // solve
    //   Mo's algorithm
    int sqrt_n = sqrt(n);
    vector<int> ixs(m);
    whole(iota, ixs, 0);
    whole(sort, ixs, [&](int i, int j) {
        return make_pair(l[i] / sqrt_n, r[i]) < make_pair(l[j] / sqrt_n, r[j]);
    });
    vector<int> acc { 0 }; whole(partial_sum, a, back_inserter(acc), bit_xor<int>());
    vector<int> accs(max(k, *whole(max_element, a)) * 2 + 3);
    ll cnt = 0;
    int l_cur = 0, r_cur = 0;
    vector<ll> ans(m);
    for (int i : ixs) {
        while (l[i] < l_cur) {
            int i = -- l_cur;
            if ((acc[i] ^ acc[r_cur]) == k) cnt += 1;
            cnt += accs[acc[i] ^ k];
            accs[acc[i]] += 1;
        }
        while (r_cur < r[i]) {
            int i = r_cur ++;
            accs[acc[i]] += 1;
            cnt += accs[acc[i+1] ^ k];
        }
        while (l_cur < l[i]) {
            int i = l_cur ++;
            accs[acc[i]] -= 1;
            cnt -= accs[acc[i] ^ k];
            if ((acc[i] ^ acc[r_cur]) == k) cnt -= 1;
        }
        while (r[i] < r_cur) {
            int i = -- r_cur;
            cnt -= accs[acc[i+1] ^ k];
            accs[acc[i]] -= 1;
        }
        ans[i] = cnt;
    }
    // output
    repeat (i,m) {
        cout << ans[i] << endl;
    }
    return 0;
}
```
