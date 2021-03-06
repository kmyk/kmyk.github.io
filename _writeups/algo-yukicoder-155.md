---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/155/
  - /blog/2016/02/19/yuki-155/
date: 2016-02-19T21:55:05+09:00
tags: [ "competitive", "writeup", "yukicoder", "dp", "inversion" ]
---

# Yukicoder No.155 生放送とBGM

## [No.155 生放送とBGM](http://yukicoder.me/problems/183)

### 解法

戻すDP。$O(N^2L)$。

非常に愚直にやると$O(N \cdot N!)$で明らかに間に合わない。

最後に使う曲を固定し、それ以外の曲で、使った曲数$\times$流れた時間$\to$そのような組み合わせの数、というDPをすれば、1曲につき$O(N^2L)$なので全体で$O(N^3L)$となる。
曲$i$以外の曲を$j$曲使って$k$秒流れるような組み合わせの数を${\rm dp}\_{i,j,k}$とすると、${\rm ans} = \Sigma_i \Sigma_j \Sigma\_{k \lt L \le k + S_i} ((j+1) \cdot j! \cdot dp\_{i,j,l} \cdot (N-j-1)!)$である。
しかしこれだと少し間に合わない。

$O(N^3L)$のDPの作製は、曲$i$以外の曲$x$に関して、${\rm dp}\_{i,j,k} \gets {\rm dp}\_{i,j,k} + {\rm dp}\_{i,j-1,k-S_x}$という代入を繰り返す。
そこで、一旦全ての曲に関して上の更新をし、除外したい曲$i$に関して逆演算をし、曲$i$に関して更新しなかった場合のDP tableを復元すればよい。これにより$O(N^2L)$となる。


### 実装

overflowしないように`double`を使っている。
dp更新部分を簡単にするため、領域を多めに確保している。

#### $O(N^2L)$

逆向きの更新の実装はとても単純で、

``` c++
repeat_reverse (j,n) {
    repeat_reverse (k,l) {
        dp[j+1][k+s[i]] += dp[j][k];
    }
}
```

と`dp`を更新しているのであれば、

``` c++
repeat (j,n) {
    repeat (k,l) {
        dp[j+1][k+s[i]] -= dp[j][k];
    }
}
```

とするだけである。

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstdio>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat_reverse(i,n) for (int i = (n)-1; (i) >= 0; --(i))
typedef long long ll;
using namespace std;
int main() {
    int n, l; cin >> n >> l;
    l *= 60;
    vector<int> s(n);
    repeat (i,n) {
        int mn, sc; char dummy; cin >> mn >> dummy >> sc;
        s[i] = mn * 60 + sc;
    }
    if (accumulate(s.begin(), s.end(), 0) <= l) {
        cout << n << endl;
        return 0;
    }
    int max_s = *max_element(s.begin(), s.end());
    vector<vector<ll> > dp(n+1, vector<ll>(l+max_s));
    dp[0][0] = 1;
    repeat (i,n) {
        repeat_reverse (j,n) {
            repeat_reverse (k,l) {
                dp[j+1][k+s[i]] += dp[j][k];
            }
        }
    }
    double acc = 0;
    vector<double> fact(n+1); fact[0] = 1; repeat (i,n) fact[i+1] = (i+1) * fact[i];
    repeat (i,n) {
        vector<vector<ll> > prv = dp;
        repeat (j,n) {
            repeat (k,l) {
                prv[j+1][k+s[i]] -= prv[j][k];
            }
        }
        repeat (j,n) {
            repeat_from (k,max(0,l-s[i]),l) {
                acc += (j+1) * fact[j] * prv[j][k] * fact[n-j-1];
            }
        }
    }
    printf("%.12lf\n", acc / fact[n]);
    return 0;
}
```

#### $O(N^3L)$ (参考)

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstdio>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat_reverse(i,n) for (int i = (n)-1; (i) >= 0; --(i))
typedef long long ll;
using namespace std;
int main() {
    int n, l; cin >> n >> l;
    l *= 60;
    vector<int> s(n);
    repeat (i,n) {
        int mn, sc; char dummy; cin >> mn >> dummy >> sc;
        s[i] = mn * 60 + sc;
    }
    if (accumulate(s.begin(), s.end(), 0) <= l) {
        cout << n << endl;
        return 0;
    }
    vector<double> fact(n+1); fact[0] = 1; repeat (i,n) fact[i+1] = (i+1) * fact[i];
    double acc = 0;
    repeat (h,n) {
        vector<vector<ll> > dp(n, vector<ll>(l));
        dp[0][0] = 1;
        repeat (i,n) if (i != h) {
            repeat_reverse (j,n-1) {
                repeat_reverse (k,max(0,l-s[i])) {
                    dp[j+1][k+s[i]] += dp[j][k];
                }
            }
        }
        repeat (j,n) {
            repeat_from (k,max(0,l-s[h]),l) {
                acc += (j+1) * fact[j] * dp[j][k] * fact[n-j-1];
            }
        }
    }
    printf("%.12lf\n", acc / fact[n]);
    return 0;
}
```
