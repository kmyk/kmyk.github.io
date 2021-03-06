---
redirect_from:
  - /writeup/algo/yukicoder/715/
layout: post
date: 2018-08-06T21:48:28+09:00
tags: [ "competitive", "writeup", "yukicoder", "grundy", "oeis" ]
"target_url": [ "https://yukicoder.me/problems/no/715" ]
---

# Yukicoder No.715 集合と二人ゲーム

## solution

OEIS: <https://oeis.org/A002187>。愚直にgrundy数を求めて周期性。$O(n \log n)$。

## note

yukicoderでこんな問題が出たらほぼ間違いなく実験して周期性かOEISでしょという経験則がある。
まあゆるふわなのでOK。

## implementation

``` c++
#include <bits/stdc++.h>
#define REP(i, n) for (int i = 0; (i) < int(n); ++ (i))
#define REP3(i, m, n) for (int i = (m); (i) < int(n); ++ (i))
#define ALL(x) begin(x), end(x)
using namespace std;

/**
 * @sa https://oeis.org/A002187
 * @note > Has period 34 with the only exceptions at n=0, 14, 16, 17, 31, 34 and 51.
 */
vector<int> make_grundy(int k) {
    vector<int> grundy(k);
    grundy[0] = 0;
    REP3 (len, 1, k) {
        vector<int> used;
        used.push_back(grundy[max(0, len - 2)]);
        REP3 (l, 3, len + 1) {
            used.push_back(grundy[l - 3] ^ grundy[len - l]);
        }
        sort(ALL(used));
        used.erase(unique(ALL(used)), used.end());
        int g = 0;
        while (g < used.size() and g == used[g]) {
            ++ g;
        }
        grundy[len] = g;
    }
    return grundy;
}

bool solve(int n, vector<int> a) {
    vector<int> table = make_grundy(34 * 3);
    auto grundy = [&](int k) { return table[k < 34 * 3 ? k : k % 34 + 34 * 2]; };

    sort(ALL(a));
    a.erase(unique(ALL(a)), a.end());

    int sum_g = 0;
    for (int l = 0; l < a.size(); ) {
        int r = l + 1;
        while (r < a.size() and a[r] - a[l] == r - l) ++ r;
        sum_g ^= grundy(r - l);
        l = r;
    }
    return sum_g;
}

int main() {
    int n; cin >> n;
    vector<int> a(n);
    REP (i, n) cin >> a[i];

    bool ans = solve(n, a);

    cout << (ans ? "First" : "Second") << endl;
    return 0;
}
```
