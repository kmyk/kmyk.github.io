---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/194/
  - /blog/2016/02/23/yuki-194/
date: 2016-02-23T23:10:25+09:00
tags: [ "competitive", "writeup", "yukicoder", "matrix", "cumulative-sum" ]
---

# Yukicoder No.194 フィボナッチ数列の理解(1)

法とるあたりで沢山のWAを生みました。

## [No.194 フィボナッチ数列の理解(1)](http://yukicoder.me/problems/381)

### 解法

#### A. $2 \le N \le 10^4, N \lt K \le 10^6$ の場合

$O(N)$。

$\Sigma\_{i \le n \lt i+K} f(n)$を更新しながら下から順に求めていく。

#### B. $2 \le N \le 30, N \lt K \le 10^{12}$ の場合

行列累乗。$O(N^3 \log K)$。

$F(K)$に関して、$N \times N$の行列 $$ {\mathbf F} = \left(
\begin{matrix}
1      & 1      & 1      & 1      & \cdots & 1      & 1      \\
1      & 0      & 0      & 0      & \cdots & 0      & 0      \\
0      & 1      & 0      & 0      & \cdots & 0      & 0      \\
0      & 0      & 1      & 0      & \cdots & 0      & 0      \\
0      & 0      & 0      & 1      & \cdots & 0      & 0      \\
\vdots & \vdots & \vdots & \vdots & \ddots & \vdots & \vdots \\
0      & 0      & 0      & 0      & \cdots & 1      & 0      \\
\end{matrix}
\right) $$ を累乗すればよい。

$S(K)$に関して、$F(i) = F(i-1) + F(i-2) + \dots + F(i-N) = S(i-1) - S(i-N-1)$であることを使って、$S(i) = F(i) + S(i-1) = 2S(i-1) - S(i-N-1)$。
これより、

$(N+1) \times (N+1)$の行列 $$ {\mathbf S} = \left(
\begin{matrix}
2      & 0      & 0      & 0      & \cdots & 0      & -1     \\
1      & 0      & 0      & 0      & \cdots & 0      & 0      \\
0      & 1      & 0      & 0      & \cdots & 0      & 0      \\
0      & 0      & 1      & 0      & \cdots & 0      & 0      \\
0      & 0      & 0      & 1      & \cdots & 0      & 0      \\
\vdots & \vdots & \vdots & \vdots & \ddots & \vdots & \vdots \\
0      & 0      & 0      & 0      & \cdots & 1      & 0      \\
\end{matrix}
\right) $$ を用いればよい。

### 実装

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
typedef long long ll;
using namespace std;

const ll mod = 1e9+7;
vector<vector<ll> > operator * (vector<vector<ll> > const & p, vector<vector<ll> > const & q) {
    int n = p.size();
    vector<vector<ll> > r(n, vector<ll>(n));
    repeat (y,n) {
        repeat (z,n) {
            repeat (x,n) {
                r[y][x] += p[y][z] * q[z][x] % mod;
                r[y][x] %= mod;
            }
        }
    }
    return r;
}
vector<ll> operator * (vector<vector<ll> > const & p, vector<ll> const & q) {
    int n = p.size();
    vector<ll> r(n);
    repeat (y,n) {
        repeat (z,n) {
            r[y] += p[y][z] * q[z] % mod;
            r[y] %= mod;
        }
    }
    return r;
}

int main() {
    int n; ll k; cin >> n >> k; -- k;
    vector<ll> a(n); repeat (i,n) cin >> a[i];
    if (n <= 30) {
        vector<vector<ll> > fe(n, vector<ll>(n));
        repeat (i,n)   fe[0][i] = 1;
        repeat (i,n-1) fe[i+1][i] = 1;
        vector<vector<ll> > se(n+1, vector<ll>(n+1));
        se[0][0] = 2;
        se[0][n] = mod - 1;
        repeat (i,n) se[i+1][i] = 1;
        vector<vector<ll> > f(n,   vector<ll>(n));   repeat (i,n)   f[i][i] = 1;
        vector<vector<ll> > s(n+1, vector<ll>(n+1)); repeat (i,n+1) s[i][i] = 1;
        for (int i = 0; (1ll<<i) <= k; ++ i) {
            if (k&(1ll<<i)) {
                f = f * fe;
                s = s * se;
            }
            fe = fe * fe;
            se = se * se;
        }
        vector<ll> x = a;
        vector<ll> y(n+1); y[0] = a[0]; repeat (i,n-1) y[i+1] = y[i] + a[i+1]; y[n] = 2*y[n-1];
        reverse(x.begin(), x.end());
        reverse(y.begin(), y.end());
        cout << (f * x)[n-1] << ' ' << (s * y)[n] << endl;
    } else if (k <= 1e6) {
        vector<ll> f(k+1); repeat (i,n) f[i] = a[i];
        ll acc = accumulate(a.begin(), a.end(), 0ll);
        ll sk = acc;
        repeat_from (i,n,k+1) {
            f[i] = acc;
            sk = (sk + f[i]) % mod;
            acc = (acc + f[i] - f[i-n] + mod) % mod;
        }
        cout << f[k] << ' ' << sk << endl;
    }
    return 0;
}
```
