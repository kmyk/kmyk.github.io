---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/444/
  - /blog/2016/11/12/yuki-444/
date: "2016-11-12T19:56:23+09:00"
tags: [ "competitive", "writeup", "yukicoder", "dp" ]
"target_url": [ "http://yukicoder.me/problems/no/444" ]
---

# Yukicoder No.444 旨味の相乗効果

## solution

いい感じに再帰的な式を立てて行列累乗。$O(n^3\log c)$。

まず、以下の制約は無視してよい。最後に適当に修正する。

>   選んだ$c$個が全て同じ種類の旨味成分であれば、相乗効果が生まれないので考えません。

$n$種類$c$個の旨味の相乗効果を$f(n,c)$のようにする。これは再帰的な形で書ける。
例えば$f(3,3) = a^3 + a^2b + ab^2 + b^3 + b^2c + bc^2 + c^3 + c^2a + ca^2 + abc$であるが、$f(3,3) = a(a^2 + ab + b^2 + bc + c^2 + ca) + b(b^2 + bc + c^2) + c(c^2) = af(3,2) + bf(2,2) + cf(1,2)$となる。

特に、$a_0, a_1, \dots$の前から$n$個を使うこととする。
一般に書くと$$f(n,c) = \sum\_{i_0 \lt n} \sum\_{i_1 \le i_0} \dots \sum\_{i\_{c-1} \le i\_{c-2}} a\_{i_0} a\_{i_1} \dots a\_{i\_{c-1}}$$である。
これは$$f(n,c) = \sum\_{i_0 \lt n} a\_{i_0} f(i_0+1, c-1)$$と変形できる。
$f(n,0) = 1$としておく。
これは線形の式であり行列で書けて、$$A = \begin{pmatrix}
0 & 0 & 0 & 0 & \dots & 0 \\\\
0 & a_0 & 0 & 0 & \dots & 0 \\\\
0 & a_0 & a_1 & 0 & \dots & 0 \\\\
0 & a_0 & a_1 & a_2 & \dots & 0 \\\\
\vdots & \vdots & \vdots & \vdots & \ddots & \vdots \\\\
0 & a_0 & a_1 & a_2 & \dots & a\_{n-1} \\\\
\end{pmatrix}$$として、$x_i = f(i, c-1)$と$x_i' = f(i, c)$に対し$x' = Ax$である。


## implementation

yukicoderでもnumpyは使えた気がしていたが、そうではなかった？

``` python
#include <iostream>
#include <vector>
#include <cassert>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
using namespace std;
ll powi(ll x, ll y, ll p) { // O(log y)
    assert (y >= 0);
    x = (x % p + p) % p;
    ll z = 1;
    for (ll i = 1; i <= y; i <<= 1) {
        if (y & i) z = z * x % p;
        x = x * x % p;
    }
    return z;
}
ll inv(ll x, ll p) { // p must be a prime, O(log p)
    assert ((x % p + p) % p != 0);
    return powi(x, p-2, p);
}

template <typename T, typename F>
T powt(T x, ll y, T unit, F f) {
    T z = unit;
    for (ll i = 1; i <= y; i <<= 1) {
        if (y & i) z = f(z, x);
        x = f(x, x);
    }
    return z;
}
const int mod = 1e9+7;
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
vector<vector<ll> > unit_matrix(int n) {
    vector<vector<ll> > e(n, vector<ll>(n));
    repeat (i,n) e[i][i] = 1;
    return e;
}
vector<vector<ll> > zero_matrix(int n) {
    vector<vector<ll> > o(n, vector<ll>(n));
    return o;
}
vector<vector<ll> > powm(vector<vector<ll> > const & f, ll k) {
    int n = f.size();
    return powt(f, k, unit_matrix(n), [&](vector<vector<ll> > const & a, vector<vector<ll> > const & b) { return a * b; });
}
int main() {
    int n; ll c; cin >> n >> c;
    vector<int> a(n); repeat (i,n) cin >> a[i];
    vector<vector<ll> > f = zero_matrix(n);
    repeat (y,n) repeat (x,y+1) f[y][x] = a[x];
    vector<ll> x(n, 1);
    vector<ll> y = powm(f, c) * x;
    ll ans = y[n-1];
    repeat (i,n) ans = (ans - powi(a[i], c, mod) + mod) % mod;
    cout << ans << endl;
    return 0;
}
```
