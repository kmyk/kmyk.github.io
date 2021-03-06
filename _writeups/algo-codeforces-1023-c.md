---
redirect_from:
  - /writeup/algo/codeforces/1023-c/
layout: post
date: 2018-08-18T02:00:04+09:00
tags: [ "competitive", "writeup", "codeforces", "parens", "construction" ]
"target_url": [ "http://codeforces.com/contest/1023/problem/C" ]
---

# Codeforces Round #504 (rated, Div. 1 + Div. 2, based on VK Cup 2018 Final): C. Bracket Subsequence

## 問題

balancedな括弧列$s$が与えられる。
$s$ の部分列 (つまり連続している必要はない) $t$ であってbalancedな括弧列であり長さが $k$ なものを構成せよ。

## solution

$s$ 中の `(` と `)` の間の対応を計算しておく。
対応する括弧のどちらか一方を使ったときもう一方も必ず使うようにすれば、balancedな括弧列という制約を崩さずに $t$ の長さを増減できる。
そのようにして $t$ を構成すればよい。
$O(n)$。

## note

これ好き。すぐ思い付けたけどあまり見ない発想な気がする

## implementation

``` c++
#include <bits/stdc++.h>
#define REP(i, n) for (int i = 0; (i) < (int)(n); ++ (i))
using namespace std;

string solve(int n, int k, string const & s) {
    vector<int> match(n); {
        stack<int> stk;
        REP (i, n) {
            if (s[i] == '(') {
                stk.push(i);
            } else {
                assert (s[i] == ')');
                assert (not stk.empty());
                int j = stk.top();
                stk.pop();
                match[j] = i;
                match[i] = j;
            }
        }
        assert (stk.empty());
    }

    vector<bool> used(n);
    assert (k % 2 == 0);
    REP (i, n) if (s[i] == '(') {
        if (k) {
            used[i] = used[match[i]] = true;
            k -= 2;
        }
    }

    string t;
    REP (i, n) if (used[i]) {
        t += s[i];
    }
    return t;
}

int main() {
    int n, k; cin >> n >> k;
    string s; cin >> s;
    cout << solve(n, k, s) << endl;
    return 0;
}
```
