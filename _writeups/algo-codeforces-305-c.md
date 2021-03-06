---
layout: post
redirect_from:
  - /writeup/algo/codeforces/305-c/
  - /blog/2015/11/21/cf-305-c/
date: 2015-11-21T15:39:08+09:00
tags: [ "competitive", "writeup", "codeforces" ]
---

# Codeforces Round #184 (Div. 2) C. Ivan and Powers of Two

難しくない普通の問題。ちょっと好きかも。

<!-- more -->

## [C. Ivan and Powers of Two](http://codeforces.com/contest/305/problem/C) {#c}

### 問題

$n$個の非負整数$a_1, a_2, \dots, a_n$が与えられる。
ここにいくつかの非負整数$a\_{n+1}, a\_{n+2}, \dots, a\_{n+k}$を加えて以下の条件を満たすようにしたい。

-   ある整数$v$が存在し、$\Sigma 2^{a_i} = 2^v-1$

加える整数の数の最小値を答えよ。

### 解法

まず、2の羃を取る必要はない。
数列内に整数$n$が2つ含まれていればその2つを取り除き$n+1$を加える、という操作を可能な限り繰り返した結果、sortした数列が$0, 1, 2, \dots, v-1, v$という形をしていればよい。

数列をsortして下から見ていき、重複があれば繰り上げ、抜けている数があれば追加し、最終的にいくつ追加したかを答えればよい。

### 実装

``` c++
#include <iostream>
#include <vector>
#include <set>
#include <functional>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
using namespace std;
int main() {
    int n; cin >> n;
    vector<ll> a(n); repeat (i,n) cin >> a[i];
    set<ll> b;
    function<void (ll)> push; push = [&](ll it) {
        if (b.count(it)) { // carry
            b.erase(it);
            push(it + 1);
        } else {
            b.insert(it);
        }
    };
    for (ll it : a) push(it);
    ll result = 0;
    ll last = -1;
    for (ll it : b) {
        result += it - last - 1;
        last = it;
    }
    cout << result << endl;
    return 0;
}
```
