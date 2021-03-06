---
layout: post
redirect_from:
  - /writeup/algo/csacademy/38-c/
  - /writeup/algo/cs-academy/38-c/
  - /blog/2017/07/20/csa-38-c/
date: "2017-07-20T03:12:01+09:00"
tags: [ "competitive", "writeup", "csacademy" ]
---

# CS Academy Round #38: C. Bounded Difference

実装が面倒。

## problem

長さ$N$の整数列$A$が与えられる。点の交換を$1$回だけして隣り合う要素の差の絶対値が高々$K$であるようにできるか、できるなら具体的に示せ。

## solution

制約を満たしていない点を列挙する。たくさんあるなら不可能。
少ないなら、任意の点$i$と制約を満たさない点$j$の対$(i, j)$の全てについて交換を試す。
交換が制約に影響するのは交換した点の周囲だけなので、$1$回の交換について制約の確認は$O(1)$にできる。
全体で$O(N)$。

## implementation

``` c++
#include <algorithm>
#include <cstdio>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_from(i, m, n) for (int i = (m); (i) < int(n); ++(i))
#define whole(f, x, ...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using namespace std;

pair<int, int> solve(int n, int k, vector<int> a) {
    auto is_valid_at = [&](int i) { return abs(a[i] - a[i + 1]) <= k; };
    vector<int> invalid;
    repeat (i, n - 1) {
        if (not is_valid_at(i)) {
            invalid.push_back(i);
        }
    }
    if (invalid.empty()) {
        return { -1, 0 }; // already valid
    } else {
        if (invalid.size() <= 4) {
            auto is_valid_around = [&](int j) {
                int l = max(0, j - 2);
                int r = min(n, j + 2);
                repeat_from (i, l, r - 1) {
                    if (not is_valid_at(i)) return false;
                }
                return true;
            };
            vector<int> is;
            for (int i : invalid) {
                is.push_back(i);
                is.push_back(i + 1);
            }
            whole(sort, is);
            is.erase(whole(unique, is), is.end());
            repeat (j, n) {
                for (int i : is) {
                    swap(a[i], a[j]);
                    bool is_valid = true;
                    if (not is_valid_around(j)) is_valid = false;
                    for (int k : is) {
                        if (not is_valid_around(k)) is_valid = false;
                    }
                    if (is_valid) return { i, j };
                    swap(a[i], a[j]);
                }
            }
        }
        return { -1, -1 }; // no valid solution
    }
}

int main() {
    int n, k; scanf("%d%d", &n, &k);
    vector<int> a(n); repeat (i, n) scanf("%d", &a[i]);
    pair<int, int> result = solve(n, k, a);
    if (result.first == -1) {
        printf("%d\n", result.second);
    } else {
        printf("%d %d\n", result.first + 1, result.second + 1);
    }
    return 0;
}
```

