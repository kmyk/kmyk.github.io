---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/440/
  - /blog/2017/02/08/yuki-440/
date: "2017-02-08T03:20:48+09:00"
tags: [ "competitive", "writeup", "yukicoder", "segment-tree" ]
"target_url": [ "https://yukicoder.me/problems/no/440" ]
---

# Yukicoder No.440 2次元チワワ問題

Yukicoderにしてはちょっと定数倍厳しいなと思ったら非想定解だったぽい。
想定解の方が綺麗。

## solution

segment木。$O(HW + Q (H \log W + W \log H))$。

縦横および向きで独立なので、ある($1$次元の)文字列の部分文字列から`cww`を数える問題としてよい。
区間に対してその区間中の`c` `w` `cw` `cww`の数をそれぞれ持っておけば、区間の情報の合成は$O(1)$、文字列の長さを$N$とするとsegment木で$O(\log N)$で`cww`の数が取れる。
縦横および向きの$2H + 2W$個のそれぞれに対してこれを$O(\log W), O(\log H)$でやればクエリごとに$O(H \log W + W \log H)$であり、間に合う。
ただし定数倍が厳しいので適当する必要があるかも。

## implementation

``` c++
#include <iostream>
#include <vector>
#include <functional>
#include <cmath>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < int(n); ++(i))
using ll = long long;
using namespace std;

template <typename T>
struct segment_tree { // on monoid
    int n;
    vector<T> a;
    function<T (T,T)> append; // associative
    T unit; // unit
    segment_tree() = default;
    segment_tree(int a_n, T a_unit, function<T (T,T)> a_append) {
        n = pow(2,ceil(log2(a_n)));
        a.resize(2*n-1, a_unit);
        unit = a_unit;
        append = a_append;
    }
    void point_update(int i, T z) {
        a[i+n-1] = z;
        for (i = (i+n)/2; i > 0; i /= 2) {
            a[i-1] = append(a[2*i-1], a[2*i]);
        }
    }
    T range_concat(int l, int r) {
        return range_concat(0, 0, n, l, r);
    }
    T range_concat(int i, int il, int ir, int l, int r) {
        if (l <= il and ir <= r) {
            return a[i];
        } else if (ir <= l or r <= il) {
            return unit;
        } else {
            return append(
                    range_concat(2*i+1, il, (il+ir)/2, l, r),
                    range_concat(2*i+2, (il+ir)/2, ir, l, r));
        }
    }
};
int nc2(int n) { return n*(n-1)/2; }

struct cww_t {
    int c, w, cw, wc, cww, wwc;
};
const cww_t unit = {};
cww_t append(cww_t const & x, cww_t const & y) {
    cww_t z;
    z.c = x.c + y.c;
    z.w = x.w + y.w;
    z.cw = x.cw + x.c * y.w + y.cw;
    z.wc = x.wc + x.w * y.c + y.wc;
    z.cww = x.cww + x.cw * y.w + x.c * nc2(y.w) + y.cww;
    z.wwc = x.wwc + nc2(x.w) * y.c + x.w * y.wc + y.wwc;
    return z;
}
int main() {
    int h, w; cin >> h >> w;
    vector<string> f(h); repeat (y,h) cin >> f[y];
    vector<segment_tree<cww_t> > segtree_hr(h, segment_tree<cww_t>(w, unit, append));
    vector<segment_tree<cww_t> > segtree_vt(w, segment_tree<cww_t>(h, unit, append));
    repeat (y,h) repeat (x,w) segtree_hr[y].point_update(x, (cww_t) { (f[y][x] == 'c'), (f[y][x] == 'w'), 0, 0 });
    repeat (x,w) repeat (y,h) segtree_vt[x].point_update(y, (cww_t) { (f[y][x] == 'c'), (f[y][x] == 'w'), 0, 0 });
    int q; cin >> q;
    while (q --) {
        int ly, lx, ry, rx; cin >> ly >> lx >> ry >> rx; -- ly; -- lx;
        ll ans = 0;
        repeat_from (y,ly,ry) { auto it = segtree_hr[y].range_concat(lx, rx); ans += it.cww + it.wwc; }
        repeat_from (x,lx,rx) { auto it = segtree_vt[x].range_concat(ly, ry); ans += it.cww + it.wwc; }
        cout << ans << endl;
    }
    return 0;
}
```
