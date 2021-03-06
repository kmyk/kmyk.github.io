---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/260/
  - /blog/2017/01/08/yuki-260/
date: "2017-01-08T17:37:15+09:00"
tags: [ "competitive", "writeup", "yukicoder", "dp" ]
"target_url": [ "http://yukicoder.me/problems/no/260" ]
---

# Yukicoder No.260 世界のなんとか3

世界のなべあつ に限らず、この手の余りと包含の桁DPは頻出なのでライブラリにしておいた。
しかしちょっと遅くなるようで悲しい。

## solution

DP。$3$で割った余り、$8$で割った余り、$3$を含むかどうか、をそれぞれ持ちながらの桁DP。$O(A + B)$。

## implementation

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <array>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using ll = long long;
using namespace std;
template <typename X, typename T> auto vectors(X x, T a) { return vector<T>(x, a); }
template <typename X, typename Y, typename Z, typename... Zs> auto vectors(X x, Y y, Z z, Zs... zs) { auto cont = vectors(y, z, zs...); return vector<decltype(cont)>(x, cont); }
// strmodin(s, a, b, m)[i][j] = the number of digits t in mod m  for  t \le s, i = t \bmod \prod a and t contains digits j \subset b
vector<vector<ll> > strmodin(string const & s, int a, vector<int> const & b, ll m) {
    int pow_b = 1 << b.size();
    array<int,10> table = {};
    repeat (d,10) {
        auto it = whole(find, b, d);
        if (it != b.end()) table[d] |= 1 << (it - b.begin());
    }
    auto cur = vectors(a, pow_b, ll());
    auto prv = vectors(a, pow_b, ll());
    int bound_i = 0, bound_j = 0;
    for (char c : s) {
        c -= '0';
        cur.swap(prv);
        repeat (d, c) cur[(bound_i * 10 + d) % a][bound_j | table[d]] += 1;
        bound_i = (bound_i * 10 + c) % a;
        bound_j |= table[c];
        repeat (i,a) repeat (j,pow_b) {
            repeat (d,10) {
                ll & it = cur[(i * 10 + d) % a][j | table[d]];
                it = (it + prv[i][j]) % m;
            }
            prv[i][j] = 0;
        }
    }
    cur[bound_i][bound_j] += 1;
    cur[bound_i][bound_j] %= m;
    return cur;
}
const int mod = 1e9+7;
ll f(string const & s) {
    auto dp = strmodin(s, 3 * 8, { 3 }, mod);
    ll cnt = 0;
    repeat (i,3*8) repeat (j,2) {
        if ((i % 3 == 0 or j) and i % 8 != 0) {
            cnt += dp[i][j];
            cnt %= mod;
        }
    }
    return cnt;
}
bool g(string const & s) {
    auto modstr = [&](ll m) {
        ll q = 0;
        for (char c : s) q = (q * 10 + (c - '0')) % m;
        return q;
    };
    return (modstr(3) == 0 or whole(count, s, '3')) and modstr(8) != 0;
}
int main() {
    string a, b; cin >> a >> b;
    cout << ((f(b) - f(a) + g(a)) % mod + mod) % mod << endl;
    return 0;
}
```
