---
layout: post
redirect_from:
  - /writeup/algo/codeforces/584/
  - /blog/2015/10/07/cf-584/
date: 2015-10-07T22:44:29+09:00
tags: [ "competitive", "writeup", "codeforces", "python", "ruby", "c++", "prime" ]
---

# Codeforces Round #324 (Div. 2)

不注意によるミスで3完。そもそも解くのも遅かった。div1復帰ならず。

<!-- more -->

## [A. Olesya and Rodion](http://codeforces.com/contest/584/problem/A) {#a}

### 問題

整数t ($ 2 \le t \le 10$)で割り切れるn桁の整数をひとつ出力せよ。

### 解法

多倍長整数を使って、n桁の整数を小さいものから試す。
$n = 1, t = 10$のみ条件を満たす数がないことに注意。

単にn回数字tを($t = 10$の場合に注意しつつ)出力するのでもよいようだ。

### 実装

``` python
#!/usr/bin/env python3
n, t = map(int,input().split())
x = 10 ** (n-1)
while x % t != 0:
    x += 1
if len(str(x)) == n:
    print(x)
else:
    print(-1)
```

## [B. Kolya and Tanya](http://codeforces.com/contest/584/problem/B) {#b}

### 問題

三角形がn個ある。三角形の頂点のそれぞれに整数$1, 2, 3$からひとつ選んで割り当てる。ただし、三角形でその3つの頂点に割り当てられた数の総和が$6$であるものが1つ以上存在しなければならない。そのような割り当て方はいくつか。

### 解法

全ての割り当て方から禁止された割り当て方を引く。

### 実装

``` python
#!/usr/bin/env python3
n = int(input())
print((27 ** n - 7 ** n) % 1000000007)
```

## [C. Marina and Vasya](http://codeforces.com/contest/584/problem/C) {#c}

### 問題

長さnの文字列$s_1, s_2$が与えられる。その両方とHamming距離がtである文字列$s_3$を作れ。

### 解法

$s_1, s_2$で一致している/していない場所の数を数え、それぞれの場合にいくつ$s_1, s_2$と違う文字を使うべきかを決め、$s_3$を構成する。$O(n)$。

### 実装

上手くやれなかった。長い。

``` c++
#include <iostream>
#include <vector>
#include <string>
#include <cassert>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat(i,n) repeat_from(i,0,n)
using namespace std;
int main() {
    int n, t; cin >> n >> t;
    string a, b; cin >> a >> b;
    int same = 0;
    repeat (i,n) if (a[i] == b[i]) same += 1;
    int diff = n - same;
    int q = diff % 2; // p + q = diff
    int p = diff - q;
    int r = 0; // r <= same
    auto f = [&]() { return p / 2 + q + r; };
    while (f() < t) {
        if (p == 0) break;
        p -= 2;
        q += 2;
    }
    while (f() < t) {
        if (r == same) break;
        r += 1;
    }
    if (f() != t) {
        cout << -1 << endl;
        return 0;
    }
    int pa = p / 2, pb = p / 2;
    string s(n, '\0');
    repeat (i,n) {
        char c = 'a';
        while (c == a[i] or c == b[i]) c += 1;
        if (a[i] == b[i]) {
            if (r) {
                r -= 1;
                s[i] = c;
            } else {
                s[i] = a[i];
            }
        } else {
            if (pa) {
                pa -= 1;
                s[i] = b[i];
            } else if (pb) {
                pb -= 1;
                s[i] = a[i];
            } else if (q) {
                q -= 1;
                s[i] = c;
            } else {
                assert (false);
            }
        }
    }
    assert (pa == 0 and pb == 0 and q == 0 and r == 0);
    cout << s << endl;
    return 0;
}
```

## [D. Dima and Lisa](http://codeforces.com/contest/584/problem/D) {#d}

### 問題

奇数nが与えられる。これを3つ以下の素数の和で表せ。

### 解法

ぐぐる。

Lemoine予想。

>   5より大きい奇数は 1 個の奇素数と 2 個の同じ素数の和で表せる

-   <https://ja.wikipedia.org/wiki/%E5%BC%B1%E3%81%84%E3%82%B4%E3%83%BC%E3%83%AB%E3%83%89%E3%83%90%E3%83%83%E3%83%8F%E4%BA%88%E6%83%B3>
-   <https://en.wikipedia.org/wiki/Lemoine%27s_conjecture>


素数の和という条件は意外と緩い。

### 実装

Eratosthenesの篩を貼り付けたらnが$10^9$と大きくそのままでは間に合わなかった。`require 'prime'`してみたかったことを思いだしrubyへ。書き直した際バグを2つ埋め込み、1つはsystem testまで発覚せずWAになった。
`Prime.each`内で`p`が`2`のとき`p + q + q == n`とならないのが原因。
必ず割り切れる仮定の下の割り算は、余りが0であることのassertを添えるようにすべきか。

``` ruby
#!/usr/bin/env ruby
require 'prime'
n = gets.chomp.to_i
if n.prime?
    puts 1
    puts n
elsif (n - 2).prime?
    puts 2
    puts "#{2} #{n-2}"
else
    Prime.each do |p|
        if p != 2
            q = (n - p) / 2
            if q.prime?
                puts 3
                puts "#{p} #{q} #{q}"
                break
            end
        end
    end
end
```
