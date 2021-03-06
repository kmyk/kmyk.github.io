---
layout: post
redirect_from:
  - /writeup/algo/aoj/2256/
  - /blog/2017/06/02/aoj-2256/
date: "2017-06-02T19:10:21+09:00"
tags: [ "competitive", "writeup", "aoj", "geometry" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2256" ]
---

# AOJ 2256: ケーキ分割問題 / Divide the Cake

幾何はつらい。解説を見てなんとか解いた。正直細部はあまり良く分かってない。

## solution

引かれる直線$y = f(x)$をそれが切り分ける点の集合$f^\ast = \\{ (x, y) \mid y \gt f(y) \\}$で分類する。
この集合の等しさで同値類を定義しておく。
左端となる点$(0, y)$を固定すると、そこを通るような直線$f$は全て同じ同値類$[f]$に入る。
この$f^\ast$が変化する点は$2$点$p, q$選んできてこれらを通る直線$p - q$と直線$x = 0$の交点。
そんな感じでやると$O(N^3)$。

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <algorithm>
#include <complex>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using namespace std;
template <class T> inline void setmax(T & a, T const & b) { a = max(a, b); }
template <class T> inline void setmin(T & a, T const & b) { a = min(a, b); }

const double eps = 1e-10;
typedef complex<double> point;
struct line { point s, t; };
double cross(point p, point q) { return imag(conj(p) * q); }
int ccw(point a, point b, point c) { double z = cross(b - a, c - a); return z > eps ? 1 : z < - eps ? -1 : 0; }
point intersection(line a, line b) {
    double p = cross(a.t - a.s, b.t - b.s);
    double q = cross(a.t - a.s, a.t - b.s);
    return (q / p) * (b.t - b.s) + b.s;
}

int main() {
    while (true) {
        // input
        int w, h, n; scanf("%d%d%d", &w, &h, &n);
        if (w == 0 and h == 0 and n == 0) break;
        vector<point> p(2*n);
        repeat (i, 2*n) {
            int x, y; scanf("%d%d", &x, &y);
            p[i] = point(x, y);
        }
        p.push_back(point(0, 0));
        p.push_back(point(0, h));
        p.push_back(point(w, 0));
        p.push_back(point(w, h));
        // solve
        auto x = [&](complex<double> const & p) { return real(p); };
        auto y = [&](complex<double> const & p) { return imag(p); };
        line left  = { point(0, 0), point(0, h) };
        line right = { point(w, 0), point(w, h) };
        auto does_split = [&](line l) {
            int cnt[3] = {};
            repeat (i, 2*n) {
                cnt[ccw(l.s, l.t, p[i]) + 1] += 1;
            }
            return cnt[0] + cnt[1] >= n and cnt[1] + cnt[2] >= n;
        };
        vector<double> events;
        repeat (j, p.size()) repeat (i, j) {
            if (x(p[i]) == x(p[j])) continue;
            line l = { p[i], p[j] };
            if (does_split(l)) {
                double y_left = y(intersection(l, left));
                if (- eps < y_left and y_left < h + eps) events.push_back(y_left);
            }
        }
        whole(sort, events);
        double result = 0;
        double yl = 0;
        repeat (i, events.size()) {
            double dy = events[i] - yl;
            if (dy < eps) continue;
            double yrr = 0;
            double yrl = h;
            repeat (j, p.size()) {
                if (x(p[j]) < eps) continue;
                line l = { point(0, yl + dy/2), p[j] };
                if (does_split(l)) {
                    double yj = y(intersection(l, right));
                    if (yj < h + eps) setmax(yrr, yj);
                    if (yj >   - eps) setmin(yrl, yj);
                }
            }
            if (yrl < yrr) {
                result += dy * (yrr - yrl);
            }
            yl = events[i];
        }
        // output
        printf("%.10lf\n", result / pow(h, 2));
    }
    return 0;
}
```
