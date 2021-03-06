---
layout: post
redirect_from:
  - /writeup/algo/codeforces/353-c/
  - /blog/2016/03/25/cf-353-c/
date: 2016-03-25T04:54:56+09:00
tags: [ "competitive", "writeup", "codeforces" ]
"target_url": [ "http://codeforces.com/contest/353/problem/C" ]
---

# Codeforces Round #205 (Div. 2) C. Find Maximum

簡単だったが印象は良い。

## 問題

整数列$a$および$0,1$の列$m$が与えられる。
$0,1$の列$x$で$x \le m$を満たすものに関し、内積$a \cdot x$の最大値を求めよ。

## 解法

貪欲な感じのDP。あるいは累積和。$O(N)$。

$x \le m$の制約がなければ$a$の項で正のものの総和が答えである。
$x_i = 0 \lt m_i = 1$な$i$があれば、それより小さい$j \lt i$に関しては、$a$の正負で決定できる。
このような$i$の位置に関して、全て試せばよい。

## 実装

``` c++
#include <iostream>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
template <class T> bool setmax(T & l, T const & r) { if (not (l < r)) return false; l = r; return true; }
using namespace std;
int main() {
    int n; cin >> n;
    vector<int> a(n); repeat (i,n) cin >> a[i];
    vector<bool> m(n); repeat (i,n) { char c; cin >> c; m[i] = c != '0'; }
    vector<ll> acc(n+1);
    repeat (i,n) acc[i+1] = acc[i] + max(0, a[i]);
    vector<ll> dp(n+1);
    repeat (i,n) {
        if (m[i]) {
            dp[i+1] = max(dp[i] + a[i], acc[i]);
        } else {
            dp[i+1] = dp[i];
        }
    }
    cout << dp[n] << endl;
    return 0;
}
```
