---
layout: post
redirect_from:
  - /writeup/algo/aoj/2510/
  - /blog/2017/12/08/aoj-2510/
date: "2017-12-08T06:25:39+09:00"
tags: [ "competitive", "writeup", "aoj", "jag-domestic", "dp", "knapsack-problem" ]
"target_url": [ "http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=2510" ]
---

# AOJ 2510. 双子の読書感想文 / Twin book report

10/19の茶会後、後輩宅で。

## solution

sortして輪唱ぽく。$O(N \log N + \sum w\_i)$。

まず本を読み終える時刻の最小化を考える。
本を読むのにかかる時間で降順にsortし、$1$人目は$1$冊目から順に、$2$人目は$2$冊目から順に読んでいく。
所用時間の降順であることが効き、$2$人目が読む最後の$1$冊(つまりもっとも時間のかかる本)でのみしか衝突の可能性はない。
衝突がないならば感想文まで含めて自明。
衝突があるときも、のような読み方が最適であるのはすぐに分かり、感想文を書くのはいい感じに隙間でやる。
特に、他の全ての本を読むのにかかる時間よりも多くの時間がかかる本があるときに衝突するので、隙間への差し込み方は単にknapsack問題を解けばよいことが分かる。

## implementation

``` c++
#include <algorithm>
#include <cstdio>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define repeat_reverse(i, n) for (int i = (n)-1; (i) >= 0; --(i))
#define whole(x) begin(x), end(x)
using namespace std;

int main() {
    while (true) {
        // input
        int n; scanf("%d", &n);
        if (n == 0) break;
        vector<pair<int, int> > books(n);
        repeat (i, n) {
            int r, w; scanf("%d%d", &r, &w);
            books[i] = { r, w };
        }
        // solve
        sort(whole(books));
        int max_r = books[n - 1].first;
        int sum_r = 0, sum_w = 0;
        for (auto book : books) {
            sum_r += book.first;
            sum_w += book.second;
        }
        int result;
        if (2 * max_r > sum_r) {
            int capacity = max_r - (sum_r - max_r);
            vector<char> dp(capacity + 1);
            dp[0] = true;
            repeat (i, n - 1) {
                int w = books[i].second;
                repeat_reverse (j, capacity + 1 - w) if (dp[j]) {
                    dp[j + w] = true;
                }
            }
            int knapsack = 0;
            repeat (j, capacity + 1) if (dp[j]) {
                knapsack = j;
            }
            result = 2 * max_r + sum_w - knapsack;
        } else {
            result = sum_r + sum_w;
        }
        // output
        printf("%d\n", result);
    }
    return 0;
}
```
