---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/233/
  - /blog/2016/01/14/yuki-233/
date: 2016-01-14T19:37:27+09:00
tags: [ "competitive", "writeup", "yukicoder" ]
---

# Yukicoder No.233 めぐるはめぐる (3)

## [No.233 めぐるはめぐる (3)](http://yukicoder.me/problems/537)

全部試せばよい。

``` c++
#include <iostream>
#include <algorithm>
#include <unordered_set>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
using namespace std;
int main() {
    int n; cin >> n;
    unordered_set<string> s;
    repeat (i,n) {
        string t; cin >> t;
        s.insert(t);
    }
    string vowels = "aaeiuu";
    do {
        string consonants = "bgmnr";
        do {
            repeat (i, 5) {
                string t;
                repeat (j, 5) {
                    if (i == j) t += vowels[5];
                    t += consonants[j];
                    t += vowels[j];
                }
                if (not s.count(t)) {
                    cout << t << endl;
                    return 0;
                }
            }
        } while (next_permutation(consonants.begin(), consonants.end()));
    } while (next_permutation(vowels.begin(), vowels.end()));
    cout << "NO" << endl;
    return 0;
}
```
