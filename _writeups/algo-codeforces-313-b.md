---
layout: post
redirect_from:
  - /writeup/algo/codeforces/313-b/
  - /blog/2015/12/03/cf-313-b/
date: 2015-12-03T22:42:27+09:00
tags: [ "codeforces", "competitive", "writeup", "cumulative-sum" ]
---

# Codeforces Round #186 (Div. 2) B. Ilya and Queries

累積和とるだけ

## [B. Ilya and Queries](http://codeforces.com/contest/313/problem/B) {#b}

``` c++
#include <iostream>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
using namespace std;
int main() {
    string s; cin >> s;
    int n = s.length();
    vector<int> a(n+1);
    repeat (i,n) a[i+1] = a[i] + (s[i] == s[i+1]);
    int m; cin >> m;
    repeat (i,m) {
        int l, r; cin >> l >> r;
        -- l; -- r;
        cout << a[r] - a[l] << endl;
    }
    return 0;
}
```
