---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/439/
  - /blog/2016/11/01/yuki-439/
date: "2016-11-01T18:26:04+09:00"
tags: [ "competitive", "writeup", "yukicoder", "graph", "tree", "dp" ]
"target_url": [ "http://yukicoder.me/problems/no/439" ]
---

# Yukicoder No.439 チワワのなる木

今週も茶会。後輩と私がたくさんyukicoderしてるので解いてない問題の共通集合から要素を取るのが面倒。

## solution

`cww`の中央の`w`に注目して木DP。$O(N)$。

頂点$i$とそこに隣接する頂点$j$について、辺$i - j$を切断してできる頂点$j$を根とする部分木中の`c` `w`の数をそれぞれ$c\_{i \to j}, w\_{i \to j}$とする。
頂点$i$に`w`が書かれているとして、これを中央の`w`として作れる`cww`の数は$\Sigma\_{j \ne k} c\_{i \to j} w\_{i \to k}$である。
これは木全体と各部分木について`c` `w`の数を数えておけば木DPで計算できる。

## implementation

DP部分の空間計算量が$O(1)$かなと思ったがそうでもなかった。

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>
#include <functional>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define whole(f,x,...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
typedef long long ll;
using namespace std;
int main() {
    int n; string s; cin >> n >> s;
    vector<vector<int> > g(n);
    repeat (i,n-1) {
        int a, b; cin >> a >> b; -- a; -- b;
        g[a].push_back(b);
        g[b].push_back(a);
    }
    int total_c = whole(count, s, 'c');
    int total_w = whole(count, s, 'w');
    ll ans = 0;
    function<pair<int, int> (int, int)> go = [&](int i, int parent) {
        vector<int> cs, ws;
        for (int j : g[i]) if (j != parent) {
            int c, w; tie(c, w) = go(j, i);
            cs.push_back(c);
            ws.push_back(w);
        }
        int acc_c = whole(accumulate, cs, 0) + (s[i] == 'c');
        int acc_w = whole(accumulate, ws, 0) + (s[i] == 'w');
        if (s[i] == 'w') {
            cs.push_back(total_c - acc_c);
            ws.push_back(total_w - acc_w);
            repeat (j, cs.size()) {
                ans += cs[j] *(ll) (total_w-1 - ws[j]);
            }
        }
        return make_pair(acc_c, acc_w);
    };
    go(0, -1);
    cout << ans << endl;
    return 0;
}
```
