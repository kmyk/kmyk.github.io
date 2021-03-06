---
layout: post
redirect_from:
  - /writeup/algo/codeforces/613-a/
  - /blog/2016/01/16/cf-613-a/
date: 2016-01-16T01:30:49+09:00
tags: [ "competitive", "writeup", "codeforces", "geometry" ]
---

# Codeforces Round #339 (Div. 1) A. Peter and Snow Blower

## [A. Peter and Snow Blower](http://codeforces.com/contest/613/problem/A) {#a}

### 解法

中心$P$と最も近い点を$Q$、遠い点を$R$とすると、$\pi \|R - P\|^2 - \pi \|Q - P\|^2$である。ただし、最も近い点$Q$は多角形の頂点とは限らないので注意。点と線分の距離の計算が必要。

### 実装

spaghetti sourceは最高。

``` c++
#include <iostream>
#include <vector>
#include <cstdio>
#include <complex>
#include <cmath>
#ifndef M_PI
#define M_PI 3.14159265358979323846
#endif
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
using namespace std;

// http://www.prefield.com/algorithm/
const double EPS = 1e-8;
typedef complex<double> P;
namespace std {
    bool operator < (const P& a, const P& b) {
        return real(a) != real(b) ? real(a) < real(b) : imag(a) < imag(b);
    }
}
double cross(const P& a, const P& b) {
    return imag(conj(a)*b);
}
double dot(const P& a, const P& b) {
    return real(conj(a)*b);
}
struct L : public vector<P> {
    L(const P &a, const P &b) {
        push_back(a); push_back(b);
    }
};
bool intersectSP(const L &s, const P &p) {
      return abs(s[0]-p)+abs(s[1]-p)-abs(s[1]-s[0]) < EPS; // triangle inequality
}
P projection(const L &l, const P &p) {
  double t = dot(p-l[0], l[0]-l[1]) / norm(l[0]-l[1]);
  return l[0] + t*(l[0]-l[1]);
}
double distanceSP(const L &s, const P &p) {
  const P r = projection(s, p);
  if (intersectSP(s, r)) return abs(r - p);
  return min(abs(s[0] - p), abs(s[1] - p));
}

int main() {
    cin.tie(nullptr);
    ios::sync_with_stdio(false);
    int n; double px, py; cin >> n >> px >> py;
    P p = { px, py };
    vector<P> qs(n);
    repeat (i,n) {
        double qx, qy; cin >> qx >> qy;
        qs[i] = { qx, qy };
    }
    double a = INFINITY, b = 0;
    repeat (i,n) {
        int j = (i+1)%n;
        b = max(b, abs(qs[i] - p));
        a = min(a, abs(qs[i] - p));
        a = min(a, distanceSP(L(qs[i], qs[j]), p));
    }
    printf("%.16lf\n", double(M_PI * (b*b - a*a)));
    return 0;
}
```
