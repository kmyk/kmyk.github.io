---
layout: post
redirect_from:
  - /writeup/algo/codeforces/336-c/
  - /blog/2015/12/25/cf-336-c/
date: 2015-12-25T20:25:21+09:00
tags: [ "competitive", "writeup", "codeforces" ]
---

# Codeforces Round #195 (Div. 2) C. Vasily the Bear and Sequence

## [C. Vasily the Bear and Sequence](http://codeforces.com/contest/336/problem/C) {#c}

### 問題

数列$a$が与えられる。
$a$の部分数列$b$に関して、$f(b)$を以下のように定める。

-   bit積に関する総乗$\& b$がちょうど$v$で割り切れるような$v$があるとき、そのような$v$の最大値
-   そのような$v$がないとき、$-1$

以下を満たす$a$の部分数列$b$を求めよ。

-   $f(b)$を最大にする。
-   そのようなものの中で、要素数$\|b\|$を最大にする。

### 解説

整数$v$を$f(b) = v$として持つような$b$が存在するかどうかは、$b = \\{ a_i \mid a_i \& 2^v \ne 0 \\}$として構成した$b$が、$f(b) = v$となるかを見ればよい。
bit積の性質から、掛ければ掛けるほど$0$になりやすくなるからである。一種の貪欲法である。
$v$の上界は小さいので全て試せばよく、このようにして求まった$v$の最大値に関して、その$v$を求めるときに作った$b$が答えである。

### 実装

``` c++
#include <iostream>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef unsigned long long ull;
using namespace std;
int main() {
    int n; cin >> n;
    vector<ull> a(n); repeat (i,n) cin >> a[i];
    int result = -1;
    repeat (v,32) {
        ull b = -1;
        for (ull it : a) if (it & (1ll << v)) b &= it;
        result = max(result, __builtin_ctz(b));
    }
    vector<ull> b;
    for (ull it : a) if (it & (1ll << result)) b.push_back(it);
    cout << b.size() << endl;
    repeat (i,b.size()) {
        if (i) cout << ' ';
        cout << b[i];
    }
    cout << endl;
    return 0;
}
```
