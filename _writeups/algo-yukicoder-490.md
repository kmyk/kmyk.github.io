---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/490/
  - /blog/2017/03/11/yuki-490/
date: "2017-03-11T00:11:02+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/no/490" ]
---

# Yukicoder No.490 yukiソート

CTFのチーム内で布教してたのに乗って参加してくれた人がいたが、問題文の不備や難易度の高さでWAして撤退してた。
運が悪かったという感じがする。

私はESPerして普通のsortをしたら外して$1$WAし間に合うことを祈りつつpythonで投げたら$1$TLE。

## solution

指示された通りにそのまま実装する。$O(N^2)$。

## implementation

``` c++
#include <iostream>
#include <vector>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
using namespace std;
int main() {
    int n; cin >> n;
    vector<int> a(n); repeat (i,n) cin >> a[i];
    repeat (i,2*n-3) {
        repeat (p,i+1) {
            int q = i - p;
            if (0 <= p and p < q and q <= n-1) {
                if (a[p] > a[q]) swap(a[p], a[q]);
            }
        }
    }
    repeat (i,n) cout << a[i] << ' ';
    cout << endl;
    return 0;
}
```
