---
redirect_from:
  - /writeup/algo/codeforces/1010-c/
layout: post
date: 2018-07-27T04:38:53+09:00
tags: [ "competitive", "writeup", "codeforces", "gcd", "group-theory" ]
"target_url": [ "http://codeforces.com/contest/1010/problem/C" ]
---

# Codeforces Round #499 (Div. 1): C. Border

## problem

$k - 1, k$回使うことなどを考えると次のように整理される:

加法群$\mathbb{Z}/k\mathbb{Z}$の部分集合$\mathbf{a} \subseteq \mathbb{Z}/k\mathbb{Z}$が与えられる。
生成される群$\langle \mathbf{a} \rangle$の要素を全て列挙せよ。

## solution

$\mathbb{Z}/k\mathbb{Z} = \langle 1 \rangle$であり、巡回群の部分群は巡回群。
$\mathbf{b} = \mathbf{a} \setminus \\{ 0 \\} \cup \\{ k \\} \subseteq \mathbb{N}$とおいて$d = \mathrm{gcd} \, \mathbf{b}$を考えれば$\langle \mathbf{a} \rangle = \langle d \rangle$。
$O(n \log k + k)$。

証明は巡回群の部分群が巡回群であることを構成的に示せばでてくる。
部分群$H \subseteq G = \langle g \rangle$をとる。
指数部分だけ見た同型対応があるので$G = \mathbb{Z}/k\mathbb{Z}$としてよい。
$d = \mathrm{gcd}(a, b)$として$\exists x \, y. \; ax + by = d$を経由して$\\{ ax + by \mid x, y \\} = d\mathbb{Z}$となる事実から、$\mathbb{Z}$の上で考えれば、ある$d \in \mathbb{N}$があって$\langle H \rangle = d\mathbb{Z} \subseteq \mathbb{Z}$。
よって$G = \mathbb{Z}/k\mathbb{Z}$の上で$\langle d\mathbb{Z} \rangle \subseteq \mathbb{Z}/k\mathbb{Z}$が巡回部分群であることを言えばよい。
$H = \langle d\mathbb{Z} \rangle = \\{ \\{ dx + ky \mid y \\} \mid x \\}$であるが、これを潰せば$\cup H = \\{ dx + ky \mid x, y \\} = \mathrm{gcd}(d, k)\mathbb{Z}$であることから、$H = \langle \mathrm{gcd}(d, k) \rangle$。
よって部分群$H$は巡回群。

## note

`bitset` で押し切ろうとしたが失敗した

## implementation

``` c++
#include <bits/stdc++.h>
#define REP(i, n) for (int i = 0; (i) < (int)(n); ++ (i))
#define ALL(x) begin(x), end(x)
using namespace std;

int main() {
    // input
    int n, k; cin >> n >> k;
    vector<int> a(n);
    REP (i, n) cin >> a[i];

    // solve
    REP (i, n) {
        a[i] %= k;
        if (a[i] == 0) {
            a[i] = k;
        }
    }
    int d = accumulate(ALL(a), k, [&](int x, int y) {
        return gcd(x, y);  // C++17
    });
    vector<int> answer;
    REP (i, k) if (i % d == 0) {
        answer.push_back(i);
    }

    // output
    cout << answer.size() << endl;
    for (int it : answer) {
        cout << it << " ";
    }
    cout << endl;
    return 0;
}
```
