---
layout: post
redirect_from:
  - /writeup/algo/etc/hoj-097-004/
  - /blog/2017/07/07/hoj-097-004/
date: "2017-07-07T23:30:09+09:00"
tags: [ "competitive", "writeup", "hoj", "square-root-decomposition", "lie", "optimization" ]
---

# Hamako Online Judge #097 ukuku09: 004 - ghoststudents2

-   <https://hoj.hamako-ths.ed.jp/onlinejudge/contest/97/problems/4>
-   <https://hoj.hamako-ths.ed.jp/onlinejudge/problems/769>

テストケースが弱いので嘘高速化で通ってしまった。

## solution

`set<int>::lower_bound`を使うと$O(Q N \log N)$。これだけだと間に合わないので平方分割して荒い範囲で検索し、それで見付からなかったやつだけに`lower_bound`を呼ぶようにする。
質問クエリだけ飛んでくる場合を考えればこれだけでは不十分だが、テストケースが弱いのでこれで通る。

## implementation

`bitset<N>`は主にコード長のためであり、`vector<bool>`で通るか否かは確認していない。

``` c++
#include <array>
#include <bitset>
#include <cstdio>
#include <set>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_from(i, m, n) for (int i = (m); (i) < int(n); ++(i))
using namespace std;

constexpr int sqrt_width = 1e3;
constexpr int max_n = 1e3;
int main() {
    int n, query; scanf("%d%d", &n, &query);
    vector<set<int> > used(n);
    array<bitset<max_n>, sqrt_width> bucket = {};
    while (query --) {
        int type; scanf("%d", &type);
        if (type == 1) {
            int a, b; scanf("%d%d", &a, &b); -- a; -- b;
            used[b].insert(a);
            bucket[a / sqrt_width][b] = true;
        } else if (type == 2) {
            int c, d; scanf("%d%d", &c, &d); -- c; // [l, r)
            bitset<max_n> found = {};
            int bucket_l = (c - 1) / sqrt_width + 1;
            int bucket_r = d / sqrt_width;
            repeat_from (i, bucket_l, bucket_r) {
                found |= bucket[i];
            }
            int result = found.count();
            repeat (i, n) if (not found[i]) {
                auto it = used[i].lower_bound(c);
                if (it != used[i].end() and *it < d) {
                    result += 1;
                }
            }
            printf("%d\n", result);
        }
    }
    return 0;
}
```
