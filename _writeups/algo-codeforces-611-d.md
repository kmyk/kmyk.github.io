---
layout: post
redirect_from:
  - /writeup/algo/codeforces/611-d/
  - /blog/2015/12/31/cf-611-d/
date: 2015-12-31T03:09:36+09:00
tags: [ "competitive", "writeup", "codeforces", "rolling-hash", "dp", "cumulative-sum" ]
---

# Good Bye 2015 D. New Year and Ancient Prophecy

初めて撃墜というものをしました。

## [D. New Year and Ancient Prophecy](http://codeforces.com/contest/611/problem/D) {#d}

### 問題

数字からなる長さ$n$の列($n \le 5000$)が与えられる。
この数字の列を適当に区切り、それぞれを10進数として解釈し、狭義単調増加な数列を作る。
そのような区切り方は何通りあるか。$10^9+7$で割った余りを答えよ。

### 解法

DP + 累積和 + rolling-hash。

${\rm dp}\_{l,r} = ``{\rm l以降で[l,r)の区間の文字列を最初の数字として使ってできる数列の数}"$として、とても愚直にdpをすると$O(n^4)$になる。
${\rm dp}\_{l,r} = \Sigma {\rm dp}\_{r,x} \; ({\rm int}[l,r) < {\rm int}[r,x))$と更新すると、$l,r,x$それぞれ$1 \dots n$を動き、${\rm int}[l,r) < {\rm int}[r,x)$の判定の部分で文字列長$n$がかかるためである。
累積和を取っておくことで$x$が落とせる。

文字列比較の際に$O(n)$かかってしますのは、`111111...111`のようなケースである。
文字列が一致していると全て文字列を捜査しなければならない。
一致していることを$O(1)$で判定できればよいため、事前に文字列の各区間のhash値を計算しておき、これが一致するなら判定を省略すればよい。
ただし、hashが衝突してしまうと当然WAとなるような解法であることには注意。

他にも色々解法は存在する。

### 実装

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat_reverse(i,n) for (int i = (n)-1; (i) >= 0; --(i))
typedef long long ll;
using namespace std;
const ll mod = 1000000007;
bool lt(string const & s, int al, int ar, int bl, int br) {
    if (ar - al != br - bl) {
        return ar - al < br - bl;
    } else {
        auto it = s.begin();
        return lexicographical_compare(it + al, it + ar, it + bl, it + br);
    }
}
int main() {
    int n; string s; cin >> n >> s;
    vector<vector<uint64_t> > hash(n+1, vector<uint64_t>(n+1));
    repeat (l,n+1) {
        const ll p = 1000000009;
        const ll q = 100000007;
        repeat_from (r,l+1,n+1) {
            hash[l][r] = (hash[l][r-1] * q % p + s[r-1] - '0') % p;
        }
    }
    vector<vector<ll> > acc(n+1, vector<ll>(n+2));
    vector<ll> dp(n+1);
    repeat_reverse (l,n+1) {
        dp.clear(); dp.resize(n+1);
        if (s[l] == '0') continue;
        repeat_from (r,l+1,n+1) {
            if (s[r] == '0') continue;
            repeat_from (x,l+(r-l)+(r-l),n+1) {
                if (hash[l][r] != hash[r][x] and lt(s, l,r, r,x)) {
                    dp[r] = acc[r][x];
                    break;
                }
            }
        }
        dp[n] = 1;
        repeat_reverse (r,n+1) {
            acc[l][r] = (acc[l][r+1] + dp[r]) % mod;
        }
    }
    ll result = 0;
    repeat (r,n+1) result = (result + dp[r]) % mod;
    cout << result << endl;
    return 0;
}
```
