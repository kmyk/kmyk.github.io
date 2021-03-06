---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/438/
  - /blog/2016/10/29/yuki-438/
date: "2016-10-29T00:24:34+09:00"
tags: [ "competitive", "writeup", "yukicoder", "construction" ]
"target_url": [ "http://yukicoder.me/problems/no/438" ]
---

# Yukicoder No.438 Cwwプログラミング入門

loop counterをloop内でうっかりdecrementしてしまっており無限loopでTLEしていた。ぎりぎりで気付いたが提出は間に合わず、終了の$2$秒後のACとなった。副作用は危険。

## solution

結果は$L = 10000$以内にしろという制約から、$O(L)$とできる。
$\|a\|, \|b\| \le \frac{L}{2}$の範囲のみ調べればよい。

定数$x, y$と関数$+, -$のみからなる数式だとみなしてよい。
整理して係数をまとめると、$ax + by = z$な$a, b$で$\|a\| + \|b\| + o(1) \le L$な$a, b$を見つければよいことになる。
euclidの互除法のあたりで丁寧にやってもよいが、$\|a\|, \|b\| \le \frac{L}{2}$なので愚直に試せばよい。

## implementation

-   引き算の順序が直感的なものの逆になっているので注意。
    -   でもこの順序でないとstackの高さが高々$2$でよくなる。
-   $x = 0$や$y = 0$は入力される。

``` c++
#include <iostream>
#include <deque>
#include <algorithm>
#include <cassert>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
typedef long long ll;
using namespace std;
ll gcd(ll a, ll b) { while (a) { b %= a; swap(a, b); } return b; }
const int limit = 10000;
string solve0(ll z) {
    return z == 0 ? "ccW" : "";
}
string solve1(ll x, ll z, char c) {
    assert (x > 0);
    if (z % x == 0) {
        int a = z / x;
        if (2*a-1 <= limit) {
            return string(a, c) + string(a-1, 'C');
        }
    }
    return "";
}
string solve2(ll x, ll y, ll z) {
    assert (x > 0);
    assert (y > 0);
    for (ll it = -limit/2; it <= limit/2; ++ it) {
        ll b = it;
        ll a = (z - y*b) / x;
        if (a*x + y*b == z) {
            deque<char> s;
            if (a > 0) {
                s.push_back('c');
                -- a;
            } else if (b > 0) {
                s.push_back('w');
                -- b;
            } else {
                s.push_back('c');
                s.push_back('c');
                s.push_back('W');
            }
            if (s.size() + 2*abs(a) + 2*abs(b) <= limit) {
                repeat (i, abs(a)) { s.push_front('c'); s.push_back(a > 0 ? 'C' : 'W'); }
                repeat (i, abs(b)) { s.push_front('w'); s.push_back(b > 0 ? 'C' : 'W'); }
                return string(s.begin(), s.end());
            }
        }
    }
    return "";
}
int main() {
    int x, y, z; cin >> x >> y >> z;
    string ans;
    if (ans.empty()) ans = solve0(z);
    if (ans.empty()) if (x != 0) ans = solve1(x, z, 'c');
    if (ans.empty()) if (y != 0) ans = solve1(y, z, 'w');
    if (ans.empty()) { int d = gcd(x, y); if (x != 0 and y != 0 and z % d == 0) ans = solve2(x / d, y / d, z / d); }
    if (ans.empty()) ans = "mourennaihasimasenn";
    cout << ans << endl;
    return 0;
}
```
