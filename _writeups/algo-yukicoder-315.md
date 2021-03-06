---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/315/
  - /blog/2017/01/08/yuki-315/
date: "2017-01-08T21:03:16+09:00"
tags: [ "competitive", "writeup", "yukicoder", "dp" ]
"target_url": [ "http://yukicoder.me/problems/no/315" ]
---

# Yukicoder No.315 世界のなんとか3.5

[No.260 世界のなんとか3](http://yukicoder.me/problems/no/260)で書いたのを使い回したらTLEってしまった。侵入的に特殊化した。

## solution

DP。$P$によらず$O(A + B)$。

$3$で割った余り、$P$で割った余り、$3$を含むかどうか、をそれぞれ持ちながらの桁DPを愚直にやると$O(P \cdot (A + B))$になって間に合わない。
$P = 8 \cdot 10^k$の形をしていること$3$と$10$が互いに素なことを使って、残り$k$桁の時点で$3$側の条件を満たしかつ$8$で割り切れるようなものの数を数え、これを$P$を無視して数えた結果から引く。

## implementation

No.260も含めて、見ている数より真に小さいことが確定しているかどうかのflagはDP tableに載せない方がすっきりするし定数倍速い。

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <cmath>
#include <array>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using ll = long long;
using namespace std;
template <typename X, typename T> auto vectors(X x, T a) { return vector<T>(x, a); }
template <typename X, typename Y, typename Z, typename... Zs> auto vectors(X x, Y y, Z z, Zs... zs) { auto cont = vectors(y, z, zs...); return vector<decltype(cont)>(x, cont); }
const int mod = 1e9+7;
ll f(string const & s, int p) {
    ll cnt = 0;
    auto cur = vectors(3*8, 2, ll());
    auto prv = vectors(3*8, 2, ll());
    int bound_i = 0, bound_j = 0;
    repeat (ix, s.length()) {
        char c = s[ix] - '0';
        cur.swap(prv);
        repeat (d, c) cur[(bound_i * 10 + d) % (3*8)][bound_j or d == 3] += 1;
        bound_i = (bound_i * 10 + c) % (3*8);
        if (c == 3) bound_j = true;
        repeat (i,3*8) repeat (j,2) {
            repeat (d,10) {
                ll & it = cur[(i * 10 + d) % (3*8)][j or d == 3];
                it = (it + prv[i][j]) % mod;
            }
            prv[i][j] = 0;
        }
        if ((p == 8 and ix+1 == s.length()) or (p == 80 and ix+2 == s.length()) or (p == 800 and ix+3 == s.length())) {
            if ((bound_i % 3 == 0 or bound_j) and bound_i % 8 == 0) {
                cnt -= 1;
            }
            repeat (i,3*8) repeat (j,2) {
                if ((i % 3 == 0 or j) and i % 8 == 0) {
                    cnt -= cur[i][j];
                }
            }
        }
    }
    cur[bound_i][bound_j] += 1;
    cur[bound_i][bound_j] %= mod;
    repeat (i,3*8) repeat (j,2) {
        if (i % 3 == 0 or j) {
            cnt += cur[i][j];
            cnt %= mod;
        }
    }
    return cnt;
}
bool g(string const & s, int p) {
    auto modstr = [&](ll m) {
        ll q = 0;
        for (char c : s) q = (q * 10 + (c - '0')) % m;
        return q;
    };
    return (modstr(3) == 0 or whole(count, s, '3')) and modstr(p) != 0;
}
int main() {
    string a, b; int p; cin >> a >> b >> p;
    cout << ((f(b, p) - f(a, p) + g(a, p)) % mod + mod) % mod << endl;
    return 0;
}
```
