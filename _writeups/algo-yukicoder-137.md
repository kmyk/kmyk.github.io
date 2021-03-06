---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/137/
  - /blog/2016/08/26/yuki-137/
date: "2016-08-26T00:36:22+09:00"
tags: [ "competitive", "writeup", "yukicoder", "dp", "digit-dp" ]
"target_url": [ "http://yukicoder.me/problems/no/137" ]
---

# Yukicoder No.137 貯金箱の焦り

解けず。解説を見れば分かるけど自力で思い付けた気がしない。
情報量を増やし強い仮定を得て、無駄な枝を根本で刈っている。

各硬貨の使用枚数を$2$進数表記して(並列)桁DPにする。
下の桁から決定していけば、使用枚数の$i$桁目を決定した時点で、その枝での合計金額の$i$桁目が固定されるのが重要。合計が$M$になりえない状態が毎回半分言えるので、毎回半分まで枝刈りができる。
硬貨の枚数$N$と額面$A_i$および使用枚数の桁数$\log M$に対し、$O(N(\Sigma_i A_i)\log N)$。

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_reverse(i,n) for (int i = (n)-1; (i) >= 0; --(i))
#define whole(f,x,...) ([&](decltype((x)) y) { return (f)(begin(y), end(y), ## __VA_ARGS__); })(x)
typedef long long ll;
using namespace std;
const int mod = 1234567891;
int main() {
    int n; ll m; cin >> n >> m;
    vector<int> a(n); repeat (i,n) cin >> a[i];
    vector<ll> dp { 1, 1 };
    m *= 2;
    while (true) {
        for (int i = m % 2; i < dp.size(); i += 2) {
            dp[i/2] = dp[i];
        }
        m /= 2;
        if (m == 0) break;
        dp.resize(dp.size() / 2);
        dp.resize(dp.size() + whole(accumulate, a, 0) + 1);
        repeat (i,n) {
            repeat_reverse (j, dp.size() - a[i]) {
                dp[j + a[i]] += dp[j];
                dp[j + a[i]] %= mod;
            }
        }
    }
    cout << dp[0] << endl;
    return 0;
}
```
