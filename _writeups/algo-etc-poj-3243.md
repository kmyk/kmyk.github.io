---
layout: post
redirect_from:
  - /writeup/algo/etc/poj-3243/
  - /blog/2016/11/22/poj-3243/
date: "2016-11-22T23:41:08+09:00"
tags: [ "competitive", "writeup", "poj", "pku", "discrete-logarithm-problem", "baby-step-giant-step", "meet-in-the-middle" ]
"target_url": [ "http://poj.org/problem?id=3243" ]
---

# PKU JudgeOnline 3709: Clever Y

離散対数問題。
[しほさんリスト](http://pastebin.com/cSfZW2yX)を埋めていて出会ったElGamal暗号の問題の部分問題として解いた。

茶会で挙げてみたが、POJがだめなせいでつらみを生やさせてしまった。

## problem

$0 \le X, Z, K \le 10^9$が与えられる。$Y = \min \\{ y \mid X^y \equiv K \pmod Z \\}$が存在するか判定し、存在するなら出力せよ。

## solution

半分全列挙。特にbaby-step giant-stepと呼ばれるもの。$O(\sqrt{n} \log{n})$。

問題の式は$g^x \equiv y \pmod{n}$。
$x$は存在すると仮定する。

適当な$H$を持ってきて$x = aH - b$と変形すると、$g^{aH-b} = {(g^H)}^ag^{-b} \equiv y \pmod{n}$。
両辺に$g^b$掛けて、${(g^H)}^a \equiv g^by \pmod{n}$となる。
法は$n$であるので、鳩の巣原理から$0 \le x \lt n$である。
$H = \lfloor \sqrt{n} \rfloor + 1$等とすると、$x = aH - b$と分解したので$0 \le a, b \lt H$とできる。

$b$の動く範囲全てについて右辺$g^by$を$(y, gy, g^2y, \dots, g^{H-1}y)$と列挙し、効率よく検索できる構造に格納する(baby-step)。
$a$の動く範囲全てについて左辺${(g^H)}^a$を計算し、この値と一致する$g^by$が存在するか検索する(giant-step)。
${(g^H)}^a \equiv g^by \pmod{n}$となるような$(a, b)$が見つかれば、$x = aH - b$が答えである。
そのようなものがなければ$x$は存在しない。
計算量に関して、$O(\sqrt{n})$個の$a$のそれぞれについて${(g^H)}^a$を計算し$O(\log{n})$かけて$g^b$の検索するので、合計で$O(\sqrt{n}\log{n})$となる。

## implementation

-   POJ用のコードなので、流用するときは適当に修正して
-   $K \ge Z$等の場合の出力は推測した

``` c++
#include <cstdio>
#include <vector>
#include <algorithm>
#include <cmath>
#include <cassert>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
// #define whole(f,x,...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
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
int enumerative_method(int g, int y, int n) {
    int h = sqrt(n) + 3;
    int ga = 1;
    repeat (a, h) {
        if (ga == y) return a;
        ga = ga *(ll) g % n;
    }
    return -1;
}
int baby_step_giant_step(int g, int y, int n) {
    assert (0 <= g and g < n);
    assert (0 <= y and y < n);
    int h = sqrt(n) + 3;
    vector<pair<int,int> > p(h+1);
    p[0] = { y, 0 };
    // repeat (b,h) p[b+1] = { p[b].first *(ll) g % n, b+1 };
    repeat (b,h) p[b+1] = make_pair(p[b].first *(ll) g % n, b+1);
    // whole(sort, p);
    sort(p.begin(), p.end());
    int gh = powi(g, h, n);
    int gah = 1;
    repeat (a,h) {
        // auto it = whole(lower_bound, p, make_pair(gah, -1));
        vector<pair<int,int> >::iterator it = lower_bound(p.begin(), p.end(), make_pair(gah, -1));
        if (a != 0 and it != p.end()) {
            int b = it->second;
            if (powi(g, a * h - b, n) == y) return a * h - b;
        }
        gah = gah *(ll) gh % n;
    }
    return -1;
}
int main() {
    while (true) {
        int x, z, k; scanf("%d%d%d\n", &x, &z, &k);
        if (x == 0 and z == 0 and k == 0) break;
        int y = -1;
        if (y == -1 and z != 0) y = enumerative_method(x % z, k % z, z);
        if (y == -1 and z != 0) y = baby_step_giant_step(x % z, k % z, z);
        if (y == -1) { printf("No Solution\n"); continue; }
        printf("%d\n", y);
    }
    return 0;
}
```
