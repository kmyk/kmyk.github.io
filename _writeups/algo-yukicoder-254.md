---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/254/
  - /blog/2016/03/02/yuki-254/
date: 2016-03-02T00:14:27+09:00
tags: [ "competitive", "writeup", "yukicoder" ]
---

# Yukicoder No.254 文字列の構成

>  -  どの隣り合う文字も相異なる．

の制約を見落としててWAった。方針は同じだったので問題はなかった。

## [No.254 文字列の構成](http://yukicoder.me/problems/676)

### 解法

大きいほうから合わせていく。

`abababababab...aba` `cdcd...cdc` `efef...efe` `ghg` みたいに作ればよい。
`ababa...`と$n$個続いたときの回文の数は手でいくつか数えてみれば規則性が分かる。

### 実装

pythonで十分だった。

``` c++
#include <iostream>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
using namespace std;
ll foo(ll n) { return n*(n+1)/2 - (n/2)*((n+1)/2); } // parens are important
int main() {
    ll n; cin >> n;
    vector<int> ans;
    while (n > 0) {
        ll i = 1;
        while (foo(i+1) <= n) ++ i;
        ans.push_back(i);
        n -= foo(i);
    }
    repeat (i,ans.size()) {
        repeat (j,ans[i]) {
            cout << char((i%13)*2+(j%2) + 'a');
        }
    }
    cout << endl;
    return 0;
}
```
