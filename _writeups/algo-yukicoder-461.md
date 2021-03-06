---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/461/
  - /blog/2016/12/13/yuki-461/
date: "2016-12-13T00:40:39+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "https://yukicoder.me/problems/no/461" ]
---

# Yukicoder No.461 三角形はいくつ？

始めてのテスターをした。思い付いた解法でいくつか解いて、気になったことを伝えた(だけ)。
偶然だったようだが、$\log N$ひとつ分で通らないような微妙な調整はけっこう好き。

## solution

`long double`を使って$O(N^2 \log N)$。
有理数$\frac{p}{q}$として持つと、$O(N^2 \log N \cdot \log \max \\{ \max A_i, \max B_i \\})$となり(実装によるかもだが)ちょっと間に合わない。

数えたい正三角形の頂点に注目する。
外側の正三角形を構成する$3$本の引かれた$N$本の線分の合わせて$N+3$本の、これら直線の交点がその頂点になりうる。$O(N^2)$個ある。
そのような頂点(とその交点を作る$2$本の線分)を固定する。
この$2$本の線分と平行でない向きの線分で、その両方と交点を持つ線分を数えれば、正三角形が列挙できる。
これは二分探索などで数えられて$O(\log N)$。
それぞれの正三角形はその頂点の数だけ重複して数えられているので、合計を$3$で割れば答え。

## 反省

実装のミス。
有理数感があったからpythonで書き始めたが、冷静になると$N = 4000$の$O(N^2 \log N)$をpythonは明らかに厳しい。

## implementation

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <array>
#include <cmath>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
typedef long long ll;
using namespace std;
const long double eps = 1e-18;
int main() {
    int n; cin >> n;
    array<vector<long double>, 3> qs = {};
    repeat (i,n) {
        int p, a, b; cin >> p >> a >> b;
        qs[p].push_back(a /(long double) (a + b));
    }
    repeat (i,3) {
        qs[i].push_back(1);
        whole(sort, qs[i]);
    }
    ll cnt = 0;
    repeat (i,3) {
        for (long double q1 : qs[(i + 1) % 3]) {
            for (long double q2 : qs[(i + 2) % 3]) {
                long double qm = max(1 - q1, 1 - q2);
                long double qc = (1 - q1) + (1 - q2);
                if (qc <= 1) {
                    int lm = whole(lower_bound, qs[i], qm - eps) - qs[i].begin();
                    int lc = whole(lower_bound, qs[i], qc - eps) - qs[i].begin();
                    cnt += qs[i].size() - lm - (abs(qs[i][lc] - qc) < eps);
                }
            }
        }
    }
    cout << cnt / 3 << endl;
    return 0;
}
```

