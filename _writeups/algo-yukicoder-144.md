---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/144/
  - /blog/2016/10/22/yuki-144/
date: "2016-10-22T13:04:26+09:00"
tags: [ "competitive", "writeup", "yukicoder", "expected-value" ]
"target_url": [ "http://yukicoder.me/problems/no/144" ]
---

# Yukicoder No.144 エラトステネスのざる

<!-- {% raw %} -->

非独立なので苦しんだ結果、実験ゲーとして解いた。
証明は[Grenacheさんのもの](http://garnacha.techblog.jp/archives/41553014.html)をかなり参考にした。

## solution

$n \ge 2$が素数として列挙されるはその約数の数を$d_n$として確率$P_n = {(1 - p)}^{d_n - 2}$になっているので足し合わせる。
$H_n = \Sigma\_{k = 1}^n \approx \log n$なので$O(N \log N)$。

証明。
数$n$を固定する。その$1,n$以外の約数を$f_1 \lt f_2 \lt \dots \lt f\_{d_n-2}$とする。
数$n$が約数$f_i$によっては消されないという事象を$A_i$とする。
条件付き確率の性質より一般に$P(A \cap B) = P(A \mid B) P(B)$であることを使う。
$P_n = P(A_1 \cap A_2 \cap \dots \cap A\_{d_n-2}) = P(A_1) P(A_2 \mid A_1) P(A_3 \mid A_1 \cap A_2) \dots P(A\_{d_n-2} \mid A_1 \cap A_2 \cap \dots \cap A\_{{d_n-2}-1})$である。
$P(A_1) = 1 - p$である。
$P(A_2 \mid A_1)$は、$f_1 \nmid f_2$なら独立なので$P(A_2) = 1 - p$、そうでないとしても$n$が$f_1$によっては消されていないという仮定$A_1$があるため$f_2$も消えておらず$P(A_2 \mid A_1) = 1 - p$。
同様にしてどの$i$を見ても、それまでの全てと独立であるか条件付き確率の仮定が使え、その時点でまだ$f_i$が使われてない場合のみ考えればよい。全ての$i$に関して$P(A_i \mid A_1 \cap \dots \cap A\_{i-1}) = 1 - p$となるので$P_n = (1 - p)^{d_n-2}$。

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <cmath>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
using namespace std;
int main() {
    int n; double p; scanf("%d%lf", &n, &p);
    double ans = 0;
    vector<int> factor(n+1, 0);
    repeat_from (i,2,n+1) {
        ans += pow(1 - p, factor[i]);
        for (int j = 2*i; j < n+1; j += i) {
            factor[j] += 1;
        }
    }
    printf("%.8lf\n", ans);
    return 0;
}
```

<!-- {% endraw %} -->
