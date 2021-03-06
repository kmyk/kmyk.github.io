---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/9/
  - /blog/2016/12/13/yuki-9/
date: "2016-12-13T18:26:18+09:00"
tags: [ "competitive", "writeup", "yukicoder", "dp" ]
"target_url": [ "http://yukicoder.me/problems/no/9" ]
---

# Yukicoder No.9 モンスターのレベル上げ

## solution

最初に戦うモンスターを全て試せばよい。`priority_queue`を使って$O(N^2 \log N)$。

## implementation

``` c++
#include <iostream>
#include <vector>
#include <queue>
#include <tuple>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
using namespace std;
template <class T> using reversed_priority_queue = priority_queue<T, vector<T>, greater<T> >;
template <class T> void setmax(T & a, T const & b) { if (a < b) a = b; }
template <class T> void setmin(T & a, T const & b) { if (b < a) a = b; }
const int inf = 1e9+7;
int main() {
    int n; cin >> n;
    vector<int> as(n); repeat (i,n) cin >> as[i];
    vector<int> bs(n); repeat (i,n) cin >> bs[i];
    int ans = inf;
    repeat (offset,n) {
        reversed_priority_queue<pair<int, int> > que;
        for (int a : as) que.emplace(a, 0);
        repeat (i,n) {
            int b = bs[(offset + i) % n];
            int lvl, cnt; tie(lvl, cnt) = que.top(); que.pop();
            que.emplace(lvl + b/2, cnt + 1);
        }
        int max_cnt = 0;
        while (not que.empty()) {
            int lvl, cnt; tie(lvl, cnt) = que.top(); que.pop();
            setmax(max_cnt, cnt);
        }
        setmin(ans, max_cnt);
    }
    cout << ans << endl;
    return 0;
}
```

<hr>

-   Thu Dec 15 14:30:50 JST 2016
    -   計算量を書き間違えてたので修正
