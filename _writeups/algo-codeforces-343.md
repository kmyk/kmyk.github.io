---
layout: post
redirect_from:
  - /writeup/algo/codeforces/343/
  - /blog/2015/10/22/cf-343/
date: 2015-10-22T23:41:29+09:00
tags: [ "codeforces", "competitive", "writeup", "greedy", "binary-search" ]
"target_url": [ "!-- more --" ]
---

# Codeforces Round #200 (Div. 1)

## [A. Rational Resistance](http://codeforces.com/contest/343/problem/A) {#a}

### 問題

$1\Omega$抵抗から始めて、$1\Omega$抵抗ひとつを直列または並列に繋ぐという操作を繰り返す。
$\frac{a}{b}\Omega$の抵抗を作るために必要な$1\Omega$抵抗の数はいくつか。

### 解法

$\frac{p}{q}\Omega$の抵抗に対し、$1\Omega$抵抗を直列に繋ぐと$\frac{p+q}{q}\Omega$、並列に繋ぐと$\frac{p}{p+q}\Omega$となり、これ以外の操作はない。
逆に、$\frac{p}{q}\Omega$の抵抗は、ある$r$が存在して、$\frac{q+r}{r}\Omega$であるか、$\frac{p}{p+r}\Omega$であるかのどちらかであり、これは$p$,$q$の大小から一意に定まる。
よって、目的の$\frac{a}{b}\Omega$抵抗から始めて、基底の$1\Omega$抵抗に至るまで、ひとつずつ$1\Omega$抵抗を外していけばよい。

### 実装

euclidの互除法ぽい

``` python
#!/usr/bin/env python3
def solve(a,b):
    assert 1 <= a and 1 <= b
    if a == 1 and b == 1:
        return 1
    else:
        a, b = max(a, b), min(a, b)
        p, q = a // b, a % b
        if q == 0:
            p, q = p-1, 1
        return p + solve(q, b)
a, b = map(int,input().split())
print(solve(a,b))
```

## [B. Alternating Current](http://codeforces.com/contest/343/problem/B) {#b}

### 問題

両端が固定された2本の紐が絡まっている。
紐の絡まり方が与えられる。
紐の交差がないようにほどけるか判定せよ。

### 解法

貪欲。未証明。

同じ記号2つの連続(`++`, `--`)は、紐が単に上にもう一方の紐の上に乗っているだけであり、取り除くことができる。3つの連続(`+++`, `---`)をまとめて取り除くことはできないことに注意。再帰的に全てこれを取り除いた後、交差がある(取り除けなかった記号が残っている)かどうかを見ればよい。

### 実装

stackに積みながら舐めると$O(n)$

``` c++
#include <iostream>
using namespace std;
int main() {
    string s; cin >> s;
    string t;
    for (char c : s) {
        if (not t.empty() and t.back() == c) {
            t.pop_back();
        } else {
            t.push_back(c);
        }
    }
    cout << (t.empty() ? "Yes" : "No") << endl;
    return 0;
}
```

## [C. Read Time](http://codeforces.com/contest/343/problem/C) {#c}

[類似問](https://beta.atcoder.jp/contests/code-festival-2015-quala/tasks/codefestival_2015_qualA_d)を[先日解いている](http://kimiyuki.net/blog/2015/09/26/codefestival-quala/#d)のに時間内のACならず。
二分探索の上限を小さくとってしまってたのが原因。
反省してます。

$h_i$や$p_i$の上限が$10^{10}$と怖いけれど、特に何も対処せずとも通った。

### 問題

HDDのトラックが直線状(循環はしない)に並んでいる。磁気ヘッドの位置(複数)と、読み出すべきトラックの位置(複数)が与えられる。
磁気ヘッドは単位時間中にトラックひとつ分移動でき、読み取りに時間はかからない。
すべて読み出すのに何秒必要か。

### 解法

全ての場所を読むのに必要な時間に関して二分探索。
ある時間で可能かどうかの判定は、どこまで読んだかの情報を持ちながら端から舐めればよい。
$O(\max\\{n, m\\} \log max\\{p_n, h_m\\})$

### 実装

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat(i,n) repeat_from(i,0,n)
typedef long long ll;
using namespace std;
bool pred(vector<ll> const & hs, vector<ll> const & ps, ll t) {
    auto p = ps.begin();
    for (ll h : hs) {
        ll q;
        if (*p < h - t) {
            return false;
        } else if (*p < h) {
            q = max(h,
                    max(h + t - 2 * (h - *p),
                        h + (t - (h - *p)) / 2));
        } else {
            q = h + t;
        }
        p = upper_bound(ps.begin(), ps.end(), q);
        if (p == ps.end()) return true;
    }
    return false;
}
int main() {
    int n, m; cin >> n >> m;
    vector<ll> h(n); repeat (i,n) cin >> h[i];
    vector<ll> p(m); repeat (i,m) cin >> p[i];
    ll low = -1, high = 2 * max(h.back(), p.back()); // (low, high]
    while (low + 1 < high) {
        ll mid = (low + high) / 2;
        (pred(h,p,mid) ? high : low) = mid;
    }
    cout << high << endl;
    return 0;
}
```
