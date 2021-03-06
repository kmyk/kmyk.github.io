---
layout: post
redirect_from:
  - /writeup/algo/codeforces/582/
  - /blog/2015/10/04/cf-582/
date: 2015-10-04T04:48:55+09:00
tags: [ "codeforces", "competitive", "writeup" ]
---

# Codeforces Round #323 (Div. 1)

ratingに関する変更後初のコンテスト。ちょうどぎりぎりdiv1だった。div1,2の参加者の比が1:10とかだったのでsystestが速かった。

<!-- more -->

## [A. GCD Table](http://codeforces.com/contest/582/problem/A) {#a}

### 問題

``` haskell
f a = shuffle $ gcd <$> a <*> a
```

の逆関数$f^{-1}$を書け。

### 解法

与えられた数列の中で大きい数から順に決定していく。

明らかに、1番大きい数は元の数列に含まれ、2番目に大きい数も含まれる。
また、その確定した2数のgcdである数(のinstance)は、元の数列には含まれなかったと分かる。

ここで、残りの数列中の数(のinstance)で最も大きい数は、元の数列に含まれる。以下繰り返し。

### 実装

``` c++
#include <iostream>
#include <vector>
#include <map>
#include <algorithm>
#include <cassert>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat(i,n) repeat_from(i,0,n)
using namespace std;
int gcd(int a, int b) {
    if (b <= a) swap(a,b);
    while (a) {
        int a0 = a;
        a = b % a0;
        b = a0;
    }
    return b;
}
vector<int> solve(int n, vector<int> g) {
    map<int,int> s;
    for (int x : g) s[x] += 1;
    vector<int> a;
    sort(g.rbegin(), g.rend());
    for (int x : g) {
        if (s[x]) {
            for (int y : a) {
                int z = gcd(x, y);
                s[z] -= 2;
                assert (s[z] >= 0);
            }
            a.push_back(x);
            s[x] -= 1;
            assert (s[x] >= 0);
        }
    }
    return a;
}
int main() {
    int n; cin >> n;
    vector<int> g(n*n); repeat (i,n*n) cin >> g[i];
    vector<int> a = solve(n, g);
    repeat (i,n) { if (i) cout << ' '; cout << a[i]; } cout << endl;
    return 0;
}
```

## [B. Once Again...](http://codeforces.com/contest/582/problem/B) {#b}

### 問題

ある数列を繰り返してできる数列の、最長(広義)増加部分列の長さを求める。

### 解法

制約は$T \le 10^7$であるが$n \le 100$と$n$は小さい。
数列中に高々$n$種類の数しか現われないので、答えとなる最長増加部分列を数列1回分ごとに区切ったとき、中央の部分の数列は同じ数のみを使うことが分かる。
両側の端から$n$個までは総当たりで計算すれば、それ以外の部分は同じ数の繰り返しであるので、答えが求まる。

### 実装

最長(狭義)増加部分列のコードを修正した際、`upper_bound`になっていた(修正前はどちらでもよかった)ところを`lower_bound`のままにしていてWA。
終了後に他人のコードとdiffをとってやっと解決した。

``` c++
#include <iostream>
#include <vector>
#include <set>
#include <map>
#include <algorithm>
#include <cassert>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat(i,n) repeat_from(i,0,n)
typedef long long ll;
using namespace std;
// longest board increasing subsequence (+ in [lb, rb])
int bounded_lis(vector<int> const & xs, int lb, int rb) {
    vector<int> l;
    for (int x : xs) if (lb <= x and x <= rb) {
        auto it = upper_bound(l.begin(), l.end(), x);
        if (it == l.end() or l.back() == x) {
            l.push_back(x);
        } else {
            *it = x;
        }
    }
    return l.size();
}
int main() {
    ll n, t; cin >> n >> t;
    vector<int> a(n); repeat (i,n) cin >> a[i];
    int mn = *min_element(a.begin(), a.end());
    int mx = *max_element(a.begin(), a.end());
    set<int> s; for (int x : a) s.insert(x);
    map<int,map<int,int> > e;
    map<int,map<int,int> > re; // reversed
    for (int x : s) {
        for (int y : s) if (x <= y) {
            e[x][y] = bounded_lis(a, x, y);
            re[y][x] = e[x][y];
        }
    }
    int last = t <= 2*n ? t : n;
    vector<map<int,ll> > l(last+1); // l[i][dst] = lis(a*i, mn, dst)
    l[0][mn] = 0;
    repeat (i,last) {
        for (auto p : l[i]) {
            for (auto q : e[p.first]) {
                l[i+1][q.first] = max(l[i+1][q.first], p.second + q.second);
            }
        }
    }
    if (last == t) {
        ll result = 0;
        for (int x : s) result = max(result, l[t][x]);
        cout << result << endl;
    } else {
        vector<map<int,ll> > r(n+1); // l[i][src] = lis(a*i, src, mx)
        r[0][mx] = 0;
        repeat (i,n) {
            for (auto p : r[i]) {
                for (auto q : re[p.first]) {
                    r[i+1][q.first] = max(r[i+1][q.first], p.second + q.second);
                }
            }
        }
        ll result = 0;
        for (int x : s) {
            result = max(result, l[n][x] + e[x][x] * (t - 2*n) + r[n][x]);
        }
        cout << result << endl;
    }
    return 0;
}
```
