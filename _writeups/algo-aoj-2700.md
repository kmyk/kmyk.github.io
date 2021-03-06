---
layout: post
redirect_from:
  - /writeup/algo/aoj/2700/
  - /blog/2017/07/12/aoj-2700/
date: "2017-07-12T00:00:26+09:00"
tags: [ "competitive", "writeup", "aoj", "jag-domestic" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2700" ]
---

# AOJ 2700: 空港コード / Airport Codes

## solution

答え$K$を全探索。愚直に`set<string>`に突っ込んで大きさが$n$か見ればよい。
$L = \max \|s\_i\|$として$O(n L \log L)$。

## implementation

``` c++
#include <algorithm>
#include <iostream>
#include <set>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f, x, ...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using namespace std;

bool is_vowel(char c) {
    switch (c) {
        case 'a':
        case 'i':
        case 'u':
        case 'e':
        case 'o':
            return true;
        default:
            return false;
    }
}
int main() {
    while (true) {
        int n; cin >> n;
        if (n == 0) break;
        vector<string> code(n);
        repeat (i, n) {
            string s; cin >> s;
            code[i] += s[0];
            repeat (j, s.length() - 1) {
                if (is_vowel(s[j])) {
                    code[i] += s[j + 1];
                }
            }
        }
        constexpr int inf = 64;
        int k = 0;
        for (; k < inf; ++ k) {
            set<string> used;
            repeat (i, n) {
                used.insert(code[i].substr(0, min<int>(code[i].length(), k)));
            }
            if (used.size() == n) {
                break;
            }
        }
        printf("%d\n", k == inf ? -1 : k);
    }
    return 0;
}
```
