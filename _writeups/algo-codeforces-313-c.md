---
layout: post
redirect_from:
  - /writeup/algo/codeforces/313-c/
  - /blog/2015/12/03/cf-313-c/
date: 2015-12-03T22:42:32+09:00
tags: [ "codeforces", "competitive", "writeup", "math", "sort" ]
---

# Codeforces Round #186 (Div. 2) C. Ilya and Matrix

sortして順位で重み付けながら足し合わせる。

## [C. Ilya and Matrix](http://codeforces.com/contest/313/problem/C) {#c}

### 解法

与えられた数列の中で、$1$番目に大きい数は$n+1$倍、$2 \dots 4$番目に大きい数は$n$倍、$5 \dots 16$番目に大きい数は$n-1$倍、という風に重み付けて総和を取ればよい。

指示された手順を見ると、ある$1$つの数は$n+1$回使われ、ある$3$つの数は$n$回使われ、ある$12$つの数は$n-1$回使われ、となっていることが分かる。
何度も使う数の選択の基準はその数の大きさと配置であるが、周囲と比較して最も大きいものが採用されること、好きに配置してよいことから、上記のように最適に総和を取ることができる。

### 実装

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <cmath>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
using namespace std;
int main() {
    cin.tie(nullptr);
    ios::sync_with_stdio(false);
    int n; cin >> n; n = log2(n)/2;
    vector<ll> a(1<<2*n); repeat (i,1<<2*n) cin >> a[i];
    sort(a.rbegin(), a.rend());
    ll result = 0;
    repeat (i,1<<2*n) {
        result += a[i] * (n - int(ceil(log2(i+1)/2)) + 1);
    }
    cout << result << endl;
    return 0;
}
```
