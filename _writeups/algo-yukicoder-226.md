---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/226/
  - /blog/2017/01/11/yuki-226/
date: "2017-01-11T15:06:49+09:00"
tags: [ "competitive", "writeup", "yukicoder", "inclusion-exclusion-principle" ]
"target_url": [ "http://yukicoder.me/problems/600" ]
---

# Yukicoder No.226 0-1パズル

## solution

結果はある種の縞模様になる。$O(HW)$。

例えば一番上の行を全て`0`に固定したとする。
このとき他のマスは一意に定まる。

```
0000000000000000
????????????????
????????????????
????????????????
????????????????
????????????????

       |
       v

0000000000000000
1111111111111111
0000000000000000
1111111111111111
0000000000000000
1111111111111111
```

一番上の行をいくつか反転させるとする。
その対応する列が全て反転する。

```
0000000111100100
????????????????
????????????????
????????????????
????????????????
????????????????

       |
       v

0000000111100100
1111111000011011
0000000111100100
1111111000011011
0000000111100100
1111111000011011
```

一番上の行の決め方は$2^W$通り。
一番上の行でなく一番左の列を固定しても向きが変わるだけで同様。
つまり候補全体は、完全なチェック模様の重複を排除して、$2^H + 2^W - 2$通り。

一番上の行の決め方で矛盾しないものを数える。
ある列の一番上を`0`にできるか`1`にできるかをそれぞれ判定し、これを掛け合わせればよい。

## implementation

``` c++
#include <iostream>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
using ll = long long;
using namespace std;
const int mod = 1e9+7;
int count1(vector<string> const & f) {
    int h = f.size();
    int w = f.front().size();
    ll acc = 1;
    repeat (x,w) {
        int sats = 0;
        repeat (p,2) {
            bool sat = true;
            repeat (y,h) {
                int clr = (x % 2) ^ p ^ (y % 2);
                if (f[y][x] != '?' and f[y][x] - '0' != clr) {
                    sat = false;
                    break;
                }
            }
            sats += sat;
        }
        acc = acc * sats % mod;
        if (not acc) break;
    }
    return acc;
}
int count2(vector<string> const & f) {
    int h = f.size();
    int w = f.front().size();
    int acc = 0;
    repeat (p,2) {
        bool sat = true;
        repeat (y,h) {
            repeat (x,w) {
                int clr = p ^ (x % 2) ^ (y % 2);
                if (f[y][x] != '?' and f[y][x] - '0' != clr) {
                    sat = false;
                    break;
                }
            }
        }
        acc += sat;
    }
    return acc;
}
vector<string> transpose(vector<string> const & f) {
    int h = f.size();
    int w = f.front().size();
    vector<string> g(w, string(h, '\0'));
    repeat (x,w) repeat (y,h) g[x][y] = f[y][x];
    return g;
}
int main() {
    int h, w; cin >> h >> w;
    vector<string> f(h); repeat (y,h) cin >> f[y];
    ll ans = 0;
    ans += count1(f);
    ans += count1(transpose(f));
    ans -= count2(f);
    ans = (ans + mod) % mod;
    cout << ans << endl;
    return 0;
}
```
