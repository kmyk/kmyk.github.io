---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/420/
  - /blog/2016/09/10/yuki-420/
date: "2016-09-10T00:22:15+09:00"
tags: [ "competitive", "writeup", "yukicoder", "embedding" ]
"target_url": [ "http://yukicoder.me/problems/no/420" ]
---

# Yukicoder No.420 mod2漸化式

何も気付かずとりあえず埋め込みを書いた(実行を眺めてる間に気付いた)のだが、さらに諸々の制約の上限等とか`int`/`long long`とかを間違えててWAが乱立した。

## solution

埋め込み。愚直に計算して求まる。$400$秒くらいだった気がする。
popcountになってる。
`__builtin_popcountll`を使えば$5$秒くらい。

## implementation

``` c++
#include <iostream>
#include <array>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
using namespace std;
int main() {
    array<int,32> cnt = {};
    array<ll, 32> acc = {};
    for (ll x = 0; x < 1ll<<31; ++ x) {
        int y = __builtin_popcountll(x);
        cnt[y] += 1;
        acc[y] += x;
    }
    repeat (i,32) {
        cout << i << ' ' << cnt[i] << ' ' << acc[i] << endl;
    }
    return 0;
}
```

``` python
#!/usr/bin/env python3
s = '''
0 1 0
1 31 2147483647
2 465 64424509410
3 4495 934155386445
4 31465 8718783606820
5 169911 58851789346035
6 736281 306029304599382
7 2629575 1275122102497425
8 7888725 4371847208562600
9 20160075 12569060724617475
10 44352165 30724370660176050
11 84672315 64521178386369705
12 141120525 117311233429763100
13 206253075 185742786263791575
14 265182525 257182319442172950
15 300540195 312292816465495725
16 300540195 333112337563195440
17 265182525 312292816465495725
18 206253075 257182319442172950
19 141120525 185742786263791575
20 84672315 117311233429763100
21 44352165 64521178386369705
22 20160075 30724370660176050
23 7888725 12569060724617475
24 2629575 4371847208562600
25 736281 1275122102497425
26 169911 306029304599382
27 31465 58851789346035
28 4495 8718783606820
29 465 934155386445
30 31 64424509410
31 1 2147483647
'''
import collections
g = collections.defaultdict(lambda: (0, 0))
for line in s.strip().splitlines():
    a, b, c = map(int, line.split())
    g[a] = (b, c)
x = int(input())
print(*g[x])
```
