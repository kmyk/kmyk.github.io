---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/528/
  - /blog/2017/06/10/yuki-528/
date: "2017-06-10T00:24:54+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/no/528" ]
---

# Yukicoder No.528 10^9と10^9+7と回文

典型感があったが苦手感もあった。

## solution

$O(\log\_{10} N)$で普通にやる。法が$10^9$と$10^9+7$とふたつあるが両方同じ。

与えられた$N$は$10$進$k$桁とする。
桁数や$N$との一致の数を順に試していく。
特に、対象となる回文数を以下のように場合分けする:

-   桁数が$k$未満
    -   先頭に$0$が使えないことを除いて普通に
-   桁数が$k$で$k$桁目の数字が$N$のそれ未満
    -   先頭に$0$が使えないかつ先頭が$N$のそれ未満
-   桁数が$k$で$k$桁目の数字が$N$のそれと同じで$\lceil \frac{k}{2} \rceil$桁目の数字が$N$のそれ未満
    -   先頭が$0$かどうか気にしなくてよいが$N$の数字未満というのは見る必要がある
-   桁数が$k$で$k$桁目の数字が$N$のそれと同じで$\lceil \frac{k}{2} \rceil$桁目の数字が$N$のそれと同じ
    -   最大まで$N$と一致させる場合。$N$を越えてしまう場合があるのでその確認が必要

## implementation

``` c++
#include <iostream>
#include <string>
#include <algorithm>
#include <cassert>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_reverse(i, n) for (int i = (n)-1; (i) >= 0; --(i))
#define whole(f, x, ...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using ll = long long;
using namespace std;

ll powmod(ll x, ll y, ll p) { // O(log y)
    assert (0 <= x and x < p);
    assert (0 <= y);
    ll z = 1;
    for (ll i = 1; i <= y; i <<= 1) {
        if (y & i) z = z * x % p;
        x = x * x % p;
    }
    return z;
}

constexpr int mod1 = 1e9;
constexpr int mod2 = 1e9+7;
int main() {
    string s; cin >> s;
    whole(reverse, s);
    int n = s.length();
    ll acc1 = 0;
    ll acc2 = 0;
    repeat (i, n-1) {
        acc1 += 9 *(ll) powmod(10, i/2, mod1);
        acc2 += 9 *(ll) powmod(10, i/2, mod2);
    }
    if (n == 1) {
        acc1 += s[0] - '0';
        acc2 += s[0] - '0';
    } else if (n == 2) {
        acc1 += min(s[0], s[1]) - '0';
        acc2 += min(s[0], s[1]) - '0';
    } else {
        acc1 += (s[n-1] - '1') * (ll) powmod(10, (n-1)/2, mod1) % mod1;
        acc2 += (s[n-1] - '1') * (ll) powmod(10, (n-1)/2, mod2) % mod2;
        for (int i = n-2; i > n/2; -- i) {
            acc1 += (s[i] - '0') *(ll) powmod(10, i-n/2, mod1) % mod1;
            acc2 += (s[i] - '0') *(ll) powmod(10, i-n/2, mod2) % mod2;
        }
        int i = n/2;
        assert (i != n-1);
        bool correction = false;
        if (n % 2 == 0) {
            repeat (di, n/2) {
                if (s[i+di] > s[i-di-1]) {
                    correction = true;
                    break;
                } else if (s[i+di] < s[i-di-1]) {
                    break;
                }
            }
        } else {
            repeat (di, n/2+1) {
                if (s[i+di] > s[i-di]) {
                    correction = true;
                    break;
                } else if (s[i+di] < s[i-di]) {
                    break;
                }
            }
        }
        acc1 += s[i] - '0' + 1 - int(correction);
        acc2 += s[i] - '0' + 1 - int(correction);
    }
    acc1 %= mod1;
    acc2 %= mod2;
    if (acc1 < 0) acc1 += mod1;
    if (acc2 < 0) acc2 += mod2;
    printf("%lld\n", acc1);
    printf("%lld\n", acc2);
    return 0;
}
```
