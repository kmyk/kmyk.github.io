---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/535/
  - /blog/2017/06/24/yuki-535/
date: "2017-06-24T01:46:05+09:00"
tags: [ "competitive", "writeup", "yukicoder", "embedding", "aws" ]
"target_url": [ "https://yukicoder.me/problems/no/535" ]
---

# Yukicoder No.535 自然数の収納方法

入力の欄を見るだけで問題文を読まずとも方針が立った。
しかし間に合わず。

ところでGPUしたら速いのだろうか。

## solution

埋め込み。計算資源で殴る。各$N$ごとに$O(N^3)$。

制約を簡単に整理して得られる愚直$O(N^4)$DPを累積和で加速して$O(N^3)$。
$N = 2000$のときに$40$秒ぐらいになる。
全体としては$m = \max N = 2000$に対して$O(m^4)$。
コンテスト時間中には間に合わなかったので$5$時間寝て待ってもよかったが、実際に本番で通せることの確認は重要だと思ったのでAWSしておいた。
c4.8xlargeで40分ほど、費用は$200$円ぐらいだった。

## implementation

``` c++
#include <cstdio>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_from(i, m, n) for (int i = (m); (i) < int(n); ++(i))
using ll = long long;
using namespace std;
template <typename X, typename T> auto vectors(X x, T a) { return vector<T>(x, a); }
template <typename X, typename Y, typename Z, typename... Zs> auto vectors(X x, Y y, Z z, Zs... zs) { auto cont = vectors(y, z, zs...); return vector<decltype(cont)>(x, cont); }

constexpr int mod = 1e9+7;
constexpr int max_n = 2000;
int main() {
#pragma omp parallel for schedule(dynamic)
    repeat_from (n, 1, max_n+1) {
        ll result = 0;
        repeat_from (a_1, 1, n+1) {
            auto acc = vectors(n+1, n+2, int());
            repeat (a_j, n+1) acc[1][a_j+1] = acc[1][a_j] + (a_j == a_1);
            repeat_from (i, 2, n+1) {
                repeat_from (a_i, 1, n+1) {
                    acc[i][a_i+1] = acc[i][a_i] + acc[i-1][min(n+1, a_i+i-1)];
                    if (acc[i][a_i+1] >= mod) acc[i][a_i+1] -= mod;
                }
            }
            result += acc[n][min(n+1, a_1+1)];
        }
        printf("%d %lld\n", n, result % mod);
    }
    return 0;
}
```

``` python
#!/usr/bin/env python3
print(dict(enumerate(map(int, '''
-1
1
2
7
44
366
3747
45228
630312
9953064
175687524
423979368
54676675
623084808
979843860
206550461
727203573
...
...
...
...
...
82376583
722462590
639068744
'''.split())))[int(input())])
```
