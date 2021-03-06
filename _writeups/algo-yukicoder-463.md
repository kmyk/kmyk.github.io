---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/463/
  - /blog/2016/12/16/yuki-463/
date: "2016-12-16T22:13:20+09:00"
tags: [ "competitive", "writeup", "yukicoder", "dp", "matrix", "gaussian-elimination" ]
"target_url": [ "http://yukicoder.me/problems/no/463" ]
---

# Yukicoder No.463 魔法使いのすごろく🎲

DPに見せかけてそうでないやつ好き。

## solution

魔法を使った後の状態は連立方程式を立てて行列。使う前の状態はDP。$O(M^3 + N)$。

折り返しがない部分について。
さいころを振れば$\mathrm{dp}(i) = \frac{1}{m} \sum\_{i+1 \le j \le i+m} (c_j + \mathrm{dp}(j))$の単純なDP。
魔法を使う場合は$\mathrm{dp}(i) = \min\_{i+1 \le j \le i+m} (c_j + \mathrm{dp}(j))$。
これらは適切にすれば$O(N)$になる。

折り返しがある部分について。
まだ魔法を使ってない場合は使ってゴールに飛べばよい。
使ってしまっている場合が問題。
$\mathrm{dp}(i)$が依存するのが$j \lt i$な$\mathrm{dp}(j)$になりうるので、整礎でなくDPでは計算できない。
しかし方程式は立っているので、これを掃き出し法などで解けば求まる。$O(M^3)$。

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <cmath>
#include <cassert>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < int(n); ++(i))
#define repeat_reverse(i,n) for (int i = (n)-1; (i) >= 0; --(i))
typedef long long ll;
using namespace std;
template <class T> void setmin(T & a, T const & b) { if (b < a) a = b; }
template <typename X, typename T> auto vectors(X x, T a) { return vector<T>(x, a); }
template <typename X, typename Y, typename Z, typename... Zs> auto vectors(X x, Y y, Z z, Zs... zs) { auto cont = vectors(y, z, zs...); return vector<decltype(cont)>(x, cont); }

using real = double;
const real eps = 1e-14;

vector<vector<real> > operator * (vector<vector<real> > const & p, vector<vector<real> > const & q) {
    int n = p.size();
    vector<vector<real> > r(n, vector<real>(n));
    repeat (y,n) repeat (z,n) repeat (x,n) r[y][x] += p[y][z] * q[z][x];
    return r;
}
vector<real> operator * (vector<vector<real> > const & p, vector<real> const & q) {
    int n = p.size();
    vector<real> r(n);
    repeat (y,n) repeat (z,n) r[y] += p[y][z] * q[z];
    return r;
}
vector<vector<real> > unit_matrix(int n) {
    vector<vector<real> > e(n, vector<real>(n));
    repeat (i,n) e[i][i] = 1;
    return e;
}
vector<vector<real> > zero_matrix(int n) {
    vector<vector<real> > o(n, vector<real>(n));
    return o;
}

vector<real> gaussian_elimination(vector<vector<real> > f, vector<real> x) {
    int n = x.size();
    repeat (y,n) {
        int pivot = y;
        while (pivot < n and abs(f[pivot][y]) < eps) ++ pivot;
        assert (pivot < n);
        swap(f[y], f[pivot]);
        x[y] /= f[y][y];
        repeat_from (x,y+1,n) f[y][x] /= f[y][y];
        f[y][y] = 1;
        repeat (ny,n) if (ny != y) {
            x[ny] -= f[ny][y] * x[y];
            repeat_from (x,y+1,n) f[ny][x] -= f[ny][y] * f[y][x];
            f[ny][y] = 0;
        }
    }
    return x;
}

int main() {
    int n, m; scanf("%d%d", &n, &m);
    vector<int> c(n); repeat (i,n-2) scanf("%d", &c[i+1]);
    vector<real> init; {
        vector<vector<real> > f = vectors(m-1, m-1, real());
        vector<real> v = vectors(m-1, real());
        repeat (y,m-1) {
            f[y][y] += m;
            repeat (x,m) {
                int z = min(y+x+1, 2*(m-1)-(y+x+1));
                if (z == m-1) continue;
                f[y][z] -= 1;
                v[y] += c[n-m+z];
            }
        }
        init = gaussian_elimination(f, v);
    }
    vector<real> dp1(n);
    repeat_reverse (i,n) {
        if (i == n-1) {
            // zero
        } else if (n-m <= i) {
            dp1[i] = init[i-(n-m)];
        } else {
            repeat (j,m) dp1[i] += (c[i+j+1] + dp1[i+j+1]) / m;
        }
    }
    vector<real> dp0(n);
    repeat_reverse (i,n) {
        if (n-m-1 <= i) {
            // zero
        } else {
            repeat (j,m) dp0[i] += (c[i+j+1] + dp0[i+j+1]) / m;
            repeat (j,m) setmin(dp0[i], c[i+j+1] + dp1[i+j+1]);
        }
    }
    printf("%.12lf\n", dp0[0]);
    return 0;
}
```
