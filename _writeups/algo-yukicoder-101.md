---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/101/
  - /blog/2016/12/13/yuki-101/
date: "2016-12-13T18:26:27+09:00"
tags: [ "competitive", "writeup", "yukicoder", "permutation", "lcm" ]
"target_url": [ "http://yukicoder.me/problems/no/101" ]
---

# Yukicoder No.101 ぐるぐる！あみだくじ！

## solution

各位置から出発して何回で戻ってくるか数えてlcm。$O(N^2)$でよい。

あみだくじは置換$\sigma$を表わす。
$\sigma^k(\vec{e}) = \vec{e}$となる最小の$k \ge 1$を求める問題。
各点$x \in N$ごとに$\sigma^{k_x}(x) = x$となる$k_x \ge 1$を求め、それらの最小公倍数が答え。

## implementation

``` c++
#include <iostream>
#include <vector>
#include <numeric>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
typedef long long ll;
using namespace std;
ll gcd(ll a, ll b) { while (a) { b %= a; swap(a, b); } return b; }
ll lcm(ll a, ll b) { return (a * b) / gcd(a,b); }
int main() {
    int n, k; cin >> n >> k;
    vector<int> f_inv(n);
    whole(iota, f_inv, 0);
    while (k --) {
        int x, y; cin >> x >> y; -- x; -- y;
        swap(f_inv[x], f_inv[y]);
    }
    vector<int> f(n); repeat (i,n) f[f_inv[i]] = i;
    vector<int> cycle(n);
    repeat (i,n) {
        int j = f[i];
        int k = 1;
        while (j != i) {
            j = f[j];
            ++ k;
        }
        cycle[i] = k;
    }
    ll s = 1;
    for (int k : cycle) s = lcm(s, k);
    cout << s << endl;
    return 0;
}
```
