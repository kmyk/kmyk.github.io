---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/91/
  - /blog/2016/09/22/yuki-91/
date: "2016-09-22T22:10:28+09:00"
tags: [ "competitive", "writeup", "yukicoder", "greedy", "binary-search" ]
"target_url": [ "http://yukicoder.me/problems/no/91" ]
---

# Yukicoder No.91 赤、緑、青の石

未証明貪欲してしまった。$O(R+G+B)$でできる。
答えで二分探索すれば怪しさはない。

``` c++
#include <iostream>
#include <algorithm>
#include <array>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) y) { return (f)(begin(y), end(y), ## __VA_ARGS__); })(x)
using namespace std;
int main() {
    array<int,3> a; repeat (i,3) cin >> a[i]; whole(sort, a);
    int ans = 0;
    ans += a[0];
    a[2] -= a[0];
    a[1] -= a[0];
    a[0] = 0;
    while (a[1] >= 1 and a[2] >= 3) {
        ans += 1;
        a[1] -= 1;
        a[2] -= 3;
        if (a[1] > a[2]) swap(a[1], a[2]);
    }
    ans += a[2] / 5;
    a[2] %= 5;
    cout << ans << endl;
    return 0;
}
```
