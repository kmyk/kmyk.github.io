---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/484/
  - /blog/2017/02/24/yuki-484/
date: "2017-02-24T22:04:52+09:00"
tags: [ "competitive", "writeup", "yukicoder", "dp", "interval-dp" ]
"target_url": [ "https://yukicoder.me/problems/no/484" ]
---

# Yukicoder No.484 収穫

解けず。解けるべきだった。

## solution

区間DP。端から使っていく。$O(N^2)$。

ある区間$[l,r)$がまだ全て使われておらずこの外は全て使われているとする。
このとき次に使うのは位置$l,r-1$のどちらか。
$l,r-1$より先に$l \lt m \lt r-1$な$m$を使ったとしても、その後$l,r-1$を両方使わなければいけないので結局そのために位置$m$を横切る。
つまり$m$は後回しでも問題ない。

## implementation

``` c++
#include <iostream>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_reverse(i,n) for (int i = (n)-1; (i) >= 0; --(i))
using namespace std;
template <class T> inline void setmin(T & a, T const & b) { a = min(a, b); }
template <typename X, typename T> auto vectors(X x, T a) { return vector<T>(x, a); }
template <typename X, typename Y, typename Z, typename... Zs> auto vectors(X x, Y y, Z z, Zs... zs) { auto cont = vectors(y, z, zs...); return vector<decltype(cont)>(x, cont); }
const int inf = 1.01e9;
int main() {
    int n; cin >> n;
    vector<int> a(n); repeat (i,n) cin >> a[i];
    auto dp = vectors(2, n+1, n+1, inf);
    dp[false][1][n] = a[0];
    dp[true][0][n-1] = a[n-1];
    repeat_reverse (len,n+1) {
        repeat (l,n-len+1) {
            int r = l + len;
            if (0 <= l-1) {
                setmin(dp[false][l][r], max(dp[false][l-1][r]+1,     a[l-1]));
                setmin(dp[false][l][r], max(dp[true ][l-1][r]+r-l+1, a[l-1]));
            }
            if (r+1 < n+1) {
                setmin(dp[true][l][r], max(dp[false][l][r+1]+r-l+1, a[r]));
                setmin(dp[true][l][r], max(dp[true ][l][r+1]+1,     a[r]));
            }
        }
    }
    int ans = inf;
    repeat (p,2) repeat (l,n+1) setmin(ans, dp[p][l][l]);
    cout << ans << endl;
    return 0;
}
```
