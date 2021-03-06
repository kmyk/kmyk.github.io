---
redirect_from:
  - /writeup/algo/codeforces/1028-e/
layout: post
date: 2018-08-29T03:40:15+09:00
tags: [ "competitive", "writeup", "codeforces", "construction" ]
"target_url": [ "http://codeforces.com/contest/1028/problem/E" ]
---

# Codeforces AIM Tech Round 5 (rated, Div. 1 + Div. 2): E. Restore Array

## 問題

列$a$に対し$b_i = (a_i \bmod a _ {i + 1})$として作られた列$b$が与えられる。
列$a$としてありえるものをひとつ復元せよ。

## 解法

雰囲気でやる。$O(N)$。

$a_i$ としてありえるのは $b_i, 2b_i + 1, 2b_i + 2, \dots$ なので $a_i \ge b_i$。
$b_i \lt b _ {i + 1}$ なものを選び $a _ {i - 1}$ のことを忘れれば $a_i = b_i$ としてよい。
$a _ {i + 1}, a _ {i + 2}$ が決まっているとき $a_i$ を決めたい。
ここで常に $a_i = b_i$ として代わりに $a _ {i + 1}$ を $a _ {i + 1} + k a _ {i + 2} \gt b_i$ で置き換えてよい。
末尾での整合性や値のoverflowが不安だが最小値から始めて停止するまでぐるぐるやれば上手くいくかもしれなくて、実際に試すと上手く行く。

補足:

<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="ja" dir="ltr">E 最小値を見付けてそこから後ろ向きに貪欲でできた (直感) (未証明) 項がすべて同じだとNO すべて0がコーナーだけどpretestに入ってて慈悲を感じた</p>&mdash; うさぎ (@a3VtYQo) <a href="https://twitter.com/a3VtYQo/status/1034154735807258627?ref_src=twsrc%5Etfw">August 27, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

証明:

<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="ja" dir="ltr">Eってmaxから左に累積和とって復元して一致するかを確認するだけですか（各差がmax以下で抑えられるので差そのものが数列に現れて、maxはそのまま使える）</p>&mdash; こうき (KokiYmgch) (@Ymgch_K) <a href="https://twitter.com/Ymgch_K/status/1034154172814221312?ref_src=twsrc%5Etfw">August 27, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## 実装

``` c++
#include <bits/stdc++.h>
#define REP(i, n) for (int i = 0; (i) < (int)(n); ++ (i))
#define REP_R(i, n) for (int i = int(n) - 1; (i) >= 0; -- (i))
#define ALL(x) begin(x), end(x)
using ll = long long;
using namespace std;

vector<ll> solve1(int n, vector<int> const & b) {
    assert (b[n - 2] < b[n - 1]);
    vector<ll> a(n);
    a[n - 1] = b[n - 1];
    a[n - 2] = b[n - 2];
    if (a[n - 2] == 0) a[n - 2] += a[n - 1];
    REP_R (i, n - 2) {
        a[i] = b[i];
        if (a[i] == 0) a[i] += a[i + 1];
        while (b[i] >= a[i + 1]) {
            a[i + 1] += a[i + 2];
        }
    }
    while (b[n - 1] >= a[0]) {
        a[0] += a[1];
    }
    return a;
}

vector<ll> solve(int n, vector<int> b) {
    if (count(ALL(b),   0 ) == n) return vector<ll>(n, 1);
    if (count(ALL(b), b[0]) == n) return vector<ll>();
    int i = min_element(ALL(b)) - b.begin();
    while (b[i] == b[(i + 1) % n]) i = (i + 1) % n;
    assert (b[i] < b[(i + 1) % n]);
    rotate(b.begin(), b.begin() + ((i + 2) % n), b.end());
    vector<ll> a = solve1(n, b);
    assert (not a.empty());
    rotate(a.begin(), a.begin() + ((n - i - 2 + n) % n), a.end());
    return a;
}

int main() {
    int n; cin >> n;
    vector<int> b(n);
    REP (i, n) cin >> b[i];
    vector<ll> a = solve(n, b);
    if (a.empty()) {
        cout << "NO" << endl;
    } else {
        cout << "YES" << endl;
        REP (i, n) cout << a[i] << ' ';
        cout << endl;
        REP (i, n) assert (b[i] == a[i] % a[(i + 1) % n]);
    }
    return 0;
}
```
