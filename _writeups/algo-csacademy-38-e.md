---
layout: post
redirect_from:
  - /writeup/algo/csacademy/38-e/
  - /writeup/algo/cs-academy/38-e/
  - /blog/2017/07/20/csa-38-e/
date: "2017-07-20T03:12:03+09:00"
tags: [ "competitive", "writeup", "csacademy", "tree", "greedy" ]
"target_url": [ "https://csacademy.com/contest/round-38/task/path-union/" ]
---

# CS Academy Round #38: E. Path Union

## problem

完全二分木を考える。各高さごとに整数$A\_i$が与えられ、その高さから高々$A\_i$個まで頂点を選んでよい。選んだ頂点から根までの辺に色を塗るとき、色が塗られる頂点の数が最大になるような選び方を答えよ。

$N = 1$のときは頂点数$3$であることや、列は$A\_1$から与えられるが$i = 1$は$2$段目を指すことに注意。

## solution

貪欲っぽく下から決めていく。$O(\sum A\_i)$。

例えば葉が$16$点あって$3$点選ぶとき、明らかに$0, 4, 8$を選びたい。
一般に葉が$2^i$点あって$A\_i$点選ぶとき、$k = \min \\{ k \mid A\_i \le 2^k \\} \le i$とすると葉を幅$2^{i - k}$の区間に分けられて、その区間からそれぞれ$1$点ずつ選びたい。
そのようにする。
葉でない段については、それより下の段から伝播してきた分については既に選ばれているものとして扱う。

## implementation

``` c++
#include <algorithm>
#include <cstdio>
#include <set>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_reverse(i, n) for (int i = (n)-1; (i) >= 0; --(i))
#define whole(f, x, ...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using ll = long long;
using namespace std;

int main() {
    // input
    int n; scanf("%d", &n);
    vector<int> a(n + 1); repeat (i, n) scanf("%d", &a[i + 1]);
    // solve
    vector<ll> result;
    set<ll> used;
    repeat_reverse (depth, n + 1) {
        ll delta = 1ll << depth;
        ll i = 0;
        repeat (iteration, a[depth]) {
            while (delta) {
                if (not used.count(i)) {
                    result.push_back((1ll << depth) + i);
                    used.insert(i);
                    break;
                }
                i += delta;
                if (i == 1ll << depth) {
                    i = 0;
                    delta /= 2;
                }
            }
        }
        set<ll> next_used;
        for (ll i : used) {
            next_used.insert(i / 2);
        }
        used = next_used;
    }
    // output
    whole(sort, result);
    for (ll v : result) {
        printf("%lld\n", v);
    }
    return 0;
}
```

