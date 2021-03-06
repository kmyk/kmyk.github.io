---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/320/
  - /blog/2016/06/22/yuki-320/
date: 2016-06-22T04:06:05+09:00
tags: [ "competitive", "writeup", "yukicoder", "branch-and-bound" ]
"target_url": [ "http://yukicoder.me/problems/no/320" ]
---

# Yukicoder No.320 眠れない夜に

## solution

上限と下限が簡単に出る。分枝限定法で殴るだけ。

## implementation

``` c++
#include <iostream>
#include <map>
#include <tuple>
typedef long long ll;
using namespace std;

const int inf = 1e9+7;
ll upper(ll a, ll b, int n) { return n ? upper(b, a+b,   n-1) : b; }
ll lower(ll a, ll b, int n) { return n ? lower(b, a+b-1, n-1) : b; }
map<tuple<ll,ll,int,ll>,int> memo;
int go(ll a, ll b, int n, ll m) {
    auto key = make_tuple(a, b, n, m);
    if (memo.count(key)) return memo[key];
    if (n == 0) return memo[key] = b == m ? 0 : inf;
    if (upper(a, b, n) < m) return memo[key] = inf;
    if (lower(a, b, n) > m) return memo[key] = inf;
    int up = go(b, a+b,   n-1, m);
    int dn = go(b, a+b-1, n-1, m) + 1;
    return memo[key] = min(up, dn);
}
int main() {
    int n; ll m; cin >> n >> m;
    int ans = go(1, 1, n-2, m);
    cout << (ans == inf ? -1 : ans) << endl;
    return 0;
}
```
