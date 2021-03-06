---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/31/
  - /blog/2016/02/17/yuki-31/
date: 2016-02-17T00:05:11+09:00
tags: [ "competitive", "writeup", "yukicoder", "dp", "ad-hoc", "lie" ]
"target_url": [ "http://yukicoder.me/problems/3" ]
---

# Yukicoder No.31 悪のミックスジュース

適当にやったら通ったという感がある。

## 解法

貪欲 + DP。$O(N^4)$。
[editorial](http://rsujskf.s602.xrea.com/?yukicoder_31)も詳しい。


>   使われない果物があってもいけません。

という制約は、全てのジュースを1個ずつ買い$V := V - N$とすれば消える。

>   $1$リットルパックを買い、その一部のみを使用することも可能です。

という文言は考えず、整数で考えればよい。

>   ミックスジュースにおいて果物$k$が占める割合$p_k$は$p_1 \ge p_2 \ge \dots \ge p_N$となる必要があり

の制約は、値段$\Sigma\_{i \le k} C_k$で容量$k$リットルのジュース$k = 1 \dots N$が売られていると考えれば無視できる。

これにより、最小費用流問題やknapsack問題に似た感じの整数計画問題が現れる。
貪欲にやると整数制約によりWAし、DPすると$O(NV)$であるのでTLEする。
しかし、大半の部分は最も効率の良いジュースを買い、残りの少しを丁寧に買えばいいことは予想できるので、制約に合わせて適当に$N^3$ほどをDPし、それ意外は貪欲にすれば通る。

## 実装

``` c++
const int l = n*n*n*sqrt(n);
```

の量は適当にする。WAを喰らったら都度増やせばいいのではと思う。


``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>
#include <climits>
#include <cmath>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
typedef long long ll;
using namespace std;
int main() {
    int n; ll v; cin >> n >> v;
    vector<ll> c(n); repeat (i,n) cin >> c[i];
    if (v <= n) {
        cout << accumulate(c.begin(), c.end(), 0ll) << endl;
        return 0;
    }
    v -= n; // the restriction, that all fruits must be used, is fulfilled now.
    vector<ll> acc(n+1); repeat (i,n) acc[i+1] = acc[i] + c[i];
    const int l = 1.2e7; // ???
    vector<ll> dp(l+1, LLONG_MAX);
    dp[0] = 0;
    repeat (i,l) {
        repeat (j,min(i+1,n)) {
            dp[i+1] = min(dp[i+1], dp[i-j] + acc[j+1]);
        }
    }
    vector<int> eff(l); repeat (i,l) eff[i] = i+1;
    sort(eff.begin(), eff.end(), [&](int a, int b) {
        return dp[a] * b < dp[b] * a; // sort by the efficiency
    });
    int e = eff.front();
    cout << acc[n] + (v / e) * dp[e] + dp[v % e] << endl;
    return 0;
}
```

<hr>

-   Tue May 31 17:06:35 JST 2016
    -   rejudgeにより落ちたので修正
-   Mon Jul  4 12:03:46 JST 2016
    -   `#include <cmath>`や`#include <numeric>`の漏れにより`CE`でrejudge食らった
-   Thu Jan  5 19:17:54 JST 2017
    -   また落ちてたのでさらに修正
