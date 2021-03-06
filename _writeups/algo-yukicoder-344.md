---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/344/
  - /blog/2016/02/17/yuki-344/
date: 2016-02-17T00:05:17+09:00
tags: [ "competitive", "writeup", "yukicoder", "dp", "periodicity", "math" ]
---

# Yukicoder No.344 ある無理数の累乗

## [No.344 ある無理数の累乗](http://yukicoder.me/problems/857)

### 感想

数学ゲー (+ DP, 周期性)。解けず。解法は[editorial](http://yukicoder.me/problems/857/editorial)が詳しい。

累乗法で対数時間で解くことをまず考えた。$a + b \sqrt 3 \; (a, b \in {\mathbb N})$とする。$a, b$を$\bmod 1000$で計算したいが、これはできなかった。無理数であるため小数点以下に無限に続くため、$a,b$の$1000$の位以上が後の$a,b$の$1,10,100$の位に影響しうる。有理数なら(循環少数になるやつでも)なんとかなりそう。正直modの話はふわっとしか理解してなかったので勉強になった。

二項定理による展開はちょっと考えたが、$1 - \sqrt 3$は出てこなかった。
数学ゲーであるという判断ができなかったのが問題だろう。
無理数を扱うのは(係数だけ考えるといった程度では)無理なのだと考えるべきであった。

### 実装

``` c++
#include <iostream>
#include <vector>
#include <map>
#include <utility>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
using namespace std;
const int mod = 1000;
int main() {
    int n; cin >> n;
    vector<int> g;
    map<pair<int,int>,int> adj;
    g.push_back(2);
    g.push_back(2);
    adj[make_pair(g[0], g[1])] = 2;
    repeat_from (i,2,n+1) {
        g.push_back((2 * g[i-1] + 2 * g[i-2]) % mod);
        pair<int,int> key = { g[i-1], g[i] };
        if (adj.count(key)) {
            int l = adj[key]; // g[p] == g[i+1]
            int r = i+1;
            int j = (n - l) % (r - l) + l;
            cout << (g[j] - (n%2 ? 0 : 1) + mod) % mod << endl;
            return 0;
        }
        adj[key] = i+1;
    }
    cout << (g[n] - (n%2 ? 0 : 1) + mod) % mod << endl;
    return 0;
}
```
