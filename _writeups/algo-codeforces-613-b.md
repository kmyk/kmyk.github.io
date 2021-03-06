---
layout: post
redirect_from:
  - /writeup/algo/codeforces/613-b/
  - /blog/2016/01/16/cf-613-b/
date: 2016-01-16T01:30:58+09:00
tags: [ "competitive", "writeup", "codeforces", "implementation" ]
---

# Codeforces Round #339 (Div. 1) B. Skills

ad hocでやるだけなのに実装がつらい。間に合わず。

## [B. Skills](http://codeforces.com/contest/613/problem/B) {#b}

### 解法

最大levelのskillの数$p$、全skill中の最低level$q$に対し、力$f = c_fp + c_mq$である。
$p$を固定しその時最良の$q$を対数時間で決定する。

### 実装

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
using namespace std;
struct skill_t { ll a; int i; };
bool operator < (skill_t a, skill_t b) { return a.a < b.a; }
int main() {
    // input
    cin.tie(nullptr);
    ios::sync_with_stdio(false);
    int n; ll max_a, cf, cm, max_m; cin >> n >> max_a >> cf >> cm >> max_m;
    auto force = [=](int p, ll q) -> ll { return p * cf + q * cm; };
    vector<skill_t> xs(n);
    repeat (i,n) { cin >> xs[i].a; xs[i].i = i; }
    sort(xs.begin(), xs.end());
    // calculate
    vector<ll> acc(n+1);
    repeat (i,n) acc[i+1] = acc[i] + xs[i].a;
    int fp = 0;
    ll fq = 0;
    repeat (p,n+1) {
        ll rest_m = max_m - (p * max_a - (acc[n] - acc[n-p]));
        if (rest_m < 0) break;
        ll low = xs[0].a, high = max_a + 1; // [low,high)
        while (low + 1 < high) {
            ll mid = (low + high) / 2;
            int i = lower_bound(xs.begin(), xs.begin() + (n - p), (skill_t){ mid, -1 }) - xs.begin();
            ll m = mid * i - acc[i];
            (m <= rest_m ? low : high) = mid;
        }
        ll q = low;
        if (force(fp,fq) < force(p,q)) {
            fp = p;
            fq = q;
        }
    }
    // output
    repeat (i,fp) xs[n-i-1].a = max_a;
    repeat (i,n) if (xs[i].a < fq) xs[i].a = fq;
    vector<ll> ys(n);
    repeat (i,n) ys[xs[i].i] = xs[i].a;
    cout << force(fp, fq) << endl;
    repeat (i,n) { if (i) cout << ' '; cout << ys[i]; } cout << endl;
    return 0;
}
```
