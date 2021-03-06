---
layout: post
redirect_from:
  - /writeup/algo/aoj/2020/
  - /blog/2017/07/07/aoj-2020/
date: "2017-07-07T19:36:45+09:00"
tags: [ "competitive", "writeup", "aoj", "jag-domestic", "implementation" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2020" ]
---

# AOJ 2020: お姫様の日本語 / Princess' Japanese

問題文が難しいがやればできる。

## solution

書いてある通り実装するだけ。頑張る。$O(N)$。
前後に番兵のようなものを置くと楽。モーラへの分解などは不要というのに気付かないと地獄を見そう。

## implementation

``` c++
#include <iostream>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
using namespace std;

bool is_one_of(char c, string const & s) {
    return s.find(c) != string::npos;
}
bool is_vowel(char c) {
    return is_one_of(c, "aiueo");
}
bool is_voiceless_consonant(char c) {
    return is_one_of(c, "ksthp");
}

int main() {
    while (true) {
        string s; cin >> s;
        if (s == "#") break;
        s = string() + "^^^^" + s + "$$$$$$$$";
        string t;
        bool is_canceled = false;
        repeat (i, s.length()) {
            if (s[i] == '^' or s[i] == '$') {
                continue;
            }
            if (is_canceled and is_vowel(s[i])) {
                t += s[i];
                is_canceled = false;
                continue;
            }
            bool is_left_voiceless_consonant_y = is_voiceless_consonant(s[i - 1]) or (is_voiceless_consonant(s[i - 2]) and s[i - 1] == 'y');
            if (is_left_voiceless_consonant_y and is_one_of(s[i], "iu") and (s[i + 1] == '$' or is_voiceless_consonant(s[i + 1]))) { // rule 1
                t += string() + "(" + s[i] + ")";
                is_canceled = true;
                continue;
            }
            if (is_left_voiceless_consonant_y and is_one_of(s[i], "ao") and is_voiceless_consonant(s[i + 1])) {
                if (s[i + 2] == s[i] or (s[i + 2] == 'y' and s[i + 3] == s[i])) { // rule 2
                    t += string() + "(" + s[i] + ")";
                    is_canceled = true;
                    continue;
                }
            }
            t += s[i];
        }
        cout << t << endl;
    }
    return 0;
}
```
