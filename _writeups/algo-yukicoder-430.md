---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/430/
  - /blog/2016/10/18/yuki-430/
date: "2016-10-18T20:22:17+09:00"
tags: [ "competitive", "writeup", "yukicoder", "rolling-hash" ]
"target_url": [ "http://yukicoder.me/problems/no/430" ]
---

# Yukicoder No.430 文字列検索

茶会で。icpcや他の用事の都合で学校さぼったのでonlineで参加した。

## solution

rolling hashを素直に使う。$O(M + N \cdot \log M \cdot \max \|C_i\|)$

## implementation

衝突回避のつもりで$10^9+7$と$10^9+9$を両方使ったが、どうやらなくても通るようだ。
$\|C_i\| \le 10$かつ$0 \le c \lt 26$であり空間が十分が狭いからだろう。

``` c++
#include <iostream>
#include <vector>
#include <set>
#include <utility>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
using namespace std;
int main() {
    // prepare
    const int limit = 10;
    const int p = 1e9+7;
    const int q = 1e9+9;
    uint64_t pow_p[limit]; pow_p[0] = 1; repeat (i, limit-1) pow_p[i+1] = pow_p[i] * p;
    uint64_t pow_q[limit]; pow_q[0] = 1; repeat (i, limit-1) pow_q[i+1] = pow_q[i] * q;
    // input
    string s; int n; cin >> s >> n;
    vector<string> cs(n); repeat (i,n) cin >> cs[i];
    // compute
    int ans = 0;
    repeat_from (len, 1, limit+1) {
        // hash c_i
        set<pair<uint64_t,uint64_t> > hashes;
        for (string c : cs) if (c.length() == len) {
            uint64_t hp = 0, hq = 0;
            for (char a : c) {
                hp = hp * p + a;
                hq = hq * q + a;
            }
            hashes.emplace(hp, hq);
        }
        // hash s
        uint64_t hp = 0, hq = 0;
        repeat (i, (int)s.length()) {
            char c = i - len >= 0 ? s[i - len] : 0;
            hp = (hp - c * pow_p[len-1]) * p + s[i];
            hq = (hq - c * pow_q[len-1]) * q + s[i];
            if (hashes.count(make_pair(hp, hq))) ans += 1;
        }
    }
    // output
    cout << ans << endl;
    return 0;
}
```
