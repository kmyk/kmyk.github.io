---
layout: post
redirect_from:
  - /writeup/algo/aoj/2315/
  - /blog/2017/06/28/aoj-2315/
date: "2017-06-28T16:44:18+09:00"
tags: [ "competitive", "writeup", "aoj", "icpc", "matrix", "gaussian-elimination" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2315" ]
---

# AOJ 2315: 影の魔女 / Shadow Witch

典型。
類問は例えば: <https://yukicoder.me/problems/no/463>

## solution

DP。
原点付近は折り返しがあるので、方程式にして解いて不動点を得る。
そうでない部分は行列累乗法で加速。
$N = 1$の場合だけ注意が必要で、このとき必ずしも原点に到着できるとは限らない。
$O(\log \|S\| N^3 K^3)$。

## implementation

``` c++
#include <cassert>
#include <cmath>
#include <cstdio>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_from(i, m, n) for (int i = (m); (i) < int(n); ++(i))
using ll = long long;
using namespace std;
template <typename X, typename T> auto vectors(X x, T a) { return vector<T>(x, a); }
template <typename X, typename Y, typename Z, typename... Zs> auto vectors(X x, Y y, Z z, Zs... zs) { auto cont = vectors(y, z, zs...); return vector<decltype(cont)>(x, cont); }

constexpr double eps = 1e-8;
template <typename T>
vector<T> gaussian_elimination(vector<vector<T> > f, vector<T> x) {
    int n = x.size();
    repeat (y, n) {
        int pivot = y;
        while (pivot < n and abs(f[pivot][y]) < eps) ++ pivot;
        if (pivot == n) continue;
        swap(f[y], f[pivot]);
        x[y] /= f[y][y];
        repeat_from (x,y+1,n) f[y][x] /= f[y][y];
        f[y][y] = 1;
        repeat (ny, n) if (ny != y) {
            x[ny] -= f[ny][y] * x[y];
            repeat_from (x,y+1,n) f[ny][x] -= f[ny][y] * f[y][x];
            f[ny][y] = 0;
        }
    }
    return x;
}
vector<vector<double> > operator * (vector<vector<double> > const & a, vector<vector<double> > const & b) {
    int n = a.size();
    vector<vector<double> > c = vectors(n, n, double());
    repeat (y,n) repeat (z,n) repeat (x,n) c[y][x] = (c[y][x] + a[y][z] * b[z][x]);
    return c;
}
vector<double> operator * (vector<vector<double> > const & a, vector<double> const & b) {
    int n = a.size();
    vector<double> c(n);
    repeat (y,n) repeat (z,n) c[y] = (c[y] + a[y][z] * b[z]);
    return c;
}
template <typename T>
T powt(T x, ll y, T unit) { // O(log y)
    assert (0 <= y);
    T z = unit;
    for (ll i = 1; i <= y; i <<= 1) {
        if (y & i) z = z * x;
        x = x * x;
    }
    return z;
}
vector<vector<double> > unit_matrix(int n) {
    vector<vector<double> > e = vectors(n, n, double());
    repeat (i,n) e[i][i] = 1;
    return e;
}

double solve(int s, int n, int k) {
    s = abs(s);
    if (n == 1) {
        if (s % k != 0) return -1;
        return s / k;
    }
    // // generate the probability
    vector<double> p(n*k+1); {
        vector<int> cur(n*k+1);
        vector<int> prv;
        cur[0] = 1;
        repeat (iteration, k) {
            cur.swap(prv);
            cur.assign(n*k+1, int());
            repeat_from (a, 1, n+1) {
                repeat_from (i, a, p.size()) {
                    cur[i] += prv[i-a];
                }
            }
        }
        repeat (i, p.size()) {
            p[i] = cur[i] /(double) pow(n, k);
        }
    }
    // // solve equations
    auto f = vectors(n*k, n*k, double());
    f[0][0] += 1;
    repeat_from (y, 1, n*k) {
        repeat_from (z, 1, n*k+1) if (y != z) {
            f[y][abs(y-z)] -= p[z];
        }
        f[y][y] += 1;
    }
    vector<double> a(n*k);
    repeat_from (y, 1, n*k) {
        a[y] = 1;
    }
    a = gaussian_elimination(f, a);
    // // exponentiation by squaring
    a.push_back(1);
    f = vectors(n*k+1, n*k+1, double());
    repeat (y, n*k) {
        if (y < n*k) {
            f[y][y+1] = 1;
        }
    }
    repeat (x, n*k+1) {
        f[n*k-1][x] = p[n*k-x];
    }
    f[n*k-1][n*k] = 1;
    f[n*k][n*k] = 1;
    f = powt(f, s, unit_matrix(n*k+1));
    a = f * a;
    return a[0];
}

int main() {
    int s, n, k; scanf("%d%d%d", &s, &n, &k);
    double result = solve(s, n, k);
    printf("%.10lf\n", result);
    return 0;
}
```
