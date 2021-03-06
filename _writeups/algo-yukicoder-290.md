---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/290/
  - /blog/2016/06/07/yuki-290/
date: 2016-06-07T18:23:24+09:00
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/335" ]
---

# Yukicoder No.290 1010

golfすると楽しくなりそう

## solution

$O(1)$。

ある程度大きい文字列であれば必ず条件を見たす整数$i,j$が存在する。
具体的には$N \le 4$のとき。
それ以下の場合は存在しないかもしれないが、$N \le 3$なので全探索が可能。

具体的には`0` `1` `01` `10` `010` `101`のみが`NO`でそれ以外が`YES`。
なので単純に比較するだけでもよい。

## implementation

あまり深く考えず全探索を投げた。

``` c++
#include <iostream>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
using namespace std;
int main() {
    int n; cin >> n;
    string s; cin >> s;
    repeat (i,n) {
        repeat_from (j,i,n) {
            int l = j-i+1;
            if (j + l < n) {
                if (s.substr(i, l) == s.substr(j+1, l)) {
                    cout << "YES" << endl;
                    return 0;
                }
            }
        }
    }
    cout << "NO" << endl;
    return 0;
}
```
