---
layout: post
redirect_from:
  - /writeup/algo/aoj/1192/
  - /blog/2017/06/27/aoj-1192/
date: "2017-06-27T23:40:45+09:00"
tags: [ "competitive", "writeup", "aoj", "icpc" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1192" ]
---

# AOJ 1192: 税率変更 / Tax Rate Changed

## solution

やるだけ。$O(s^2)$。
ただし以下に注意すること。$s$円以上はだめっぽい。

>   商品の税抜価格として 1 円から s-1 円のすべてを考慮に入れよ．

## implementation

後から指摘されたが、$s-1$が範囲に入っていない。バグに見えるが通っているのでそのままとする。

``` c++
#include <algorithm>
#include <cstdio>
#define repeat_from(i, m, n) for (int i = (m); (i) < int(n); ++(i))
using namespace std;
template <class T> inline void setmax(T & a, T const & b) { a = max(a, b); }

int main() {
    while (true) {
        int x, y, s; scanf("%d%d%d", &x, &y, &s);
        if (x == 0 and y == 0 and s == 0) break;
        int result = 0;
        repeat_from (a, 1, s-1) {
            repeat_from (b, 1, s-1) {
                if (a * (100 + x) / 100 + b * (100 + x) / 100 == s) {
                    setmax(result, a * (100 + y) / 100 + b * (100 + y) / 100);
                }
            }
        }
        printf("%d\n", result);
    }
    return 0;
}
```

<hr>

-   2017年  6月 28日 水曜日 11:36:59 JST
    -   バグ
