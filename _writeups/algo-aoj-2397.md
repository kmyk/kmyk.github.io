---
layout: post
redirect_from:
  - /writeup/algo/aoj/2397/
  - /blog/2016/07/14/aoj-2397/
date: "2016-07-14T19:26:20+09:00"
tags: [ "competitive", "writeup", "aoj", "matrix", "exponentiation-by-squaring" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2397" ]
---

# AOJ 2397. Three-way Branch

## problem

$W \times H$のマスがある。$W \le 75, H \le 10^{18}$である。左上$(1,1)$から始めて、$(x,y)$から$(x+\delta,y+1), \delta = -1, 0, 1$のいずれかへ移動することを繰り返したとき、右下$(W,H)$への経路の数を答えよ。
ただし$N$箇所の通行不能マスがある。

## solution

行列の繰り返し二乗法。障害物のある場所はまじめにやる。$O(NW^3\log H)$。

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <map>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
using namespace std;

const int mod = 1e9+9;

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

vector<vector<ll> > mul(vector<vector<ll> > const & p, vector<vector<ll> > const & q) { return p * q; }
template <typename T, typename F>
T powt(T x, ll y, T unit, F f) {
    T z = unit;
    for (ll i = 1; i <= y; i <<= 1) {
        if (y & i) z = f(z, x);
        x = f(x, x);
    }
    return z;
}

int main() {
    for (int testcase = 1; ; ++ testcase) {
        // input
        int w; ll h; int n; scanf("%d%lld%d", &w, &h, &n);
        if (w == 0 and h == 0 and n == 0) break;
        map<ll,vector<int> > obsts;
        repeat (i,n) {
            int x; ll y; scanf("%d%lld", &x, &y);
            -- x; -- y;
            obsts[y].push_back(x);
        }
        // compute
        vector<vector<ll> > f = zero_matrix(w);
        repeat (i,w) {
            for (int j : { i-1, i, i+1 }) if (0 <= j and j < w) {
                f[i][j] = 1;
            }
        }
        vector<ll> x(w);
        x[0] = 1;
        ll y = 0;
        obsts[h-1].push_back(-1);
        for (auto it : obsts) {
            ll ny = it.first;
            x = powt(f, ny-y, unit_matrix(w), mul) * x;
            for (int obst : it.second) if (obst != -1) x[obst] = 0;
            y = ny;
        }
        // output
        printf("Case %d: %lld\n", testcase, x[w-1]);
    }
    return 0;
}
```
