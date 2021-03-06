---
layout: post
redirect_from:
  - /writeup/algo/csacademy/39-e/
  - /writeup/algo/cs-academy/39-e/
  - /blog/2017/07/27/csa-39-e/
date: "2017-07-27T03:04:14+09:00"
tags: [ "competitive", "writeup", "csacademy", "square-root-decomposition", "implementation", "insertion-sort" ]
"target_url": [ "https://csacademy.com/contest/round-39/task/k-swap/" ]
---

# CS Academy Round #39: E. K Swap

好き。div 2 onlyとはいえ自明問だけだと全完続出なので置かれた問題に見える。

## solution

愚直にはほとんどinsertion sortをすればよい。列に単調性がないので動かせる範囲の判定と動かす位置の決定には注意。例えば$(3, 1, 2)$と並んでいて$K = 1$のとき。しかしそれだと当然$O(N^2)$で$7$倍ぐらい間に合わないので平方分割。区間の大きさや数が動的にずれるが頑張って実装。$O(N \sqrt{N})$。

$N$要素それぞれについて区間ふたつを丸々舐めて$2\sqrt{N}$と区間を渡るので$\sqrt{N}$、合わせても$3\sqrt{N}$。それぞれの区間の長さを高々$\sqrt{N}$に制限し越えそうならふたつに分割。分割は多くても$\sqrt{N}$要素にひとつなので問題でなく、区間が増えすぎることもない。
なので全体で$O(N \sqrt{N})$。

すごい木があれば一発だと思う。

## implementation

``` c++
#include <algorithm>
#include <cassert>
#include <cmath>
#include <cstdio>
#include <memory>
#include <tuple>
#include <vector>
#define repeat(i, n) for (int i = 0; (i) < int(n); ++(i))
#define whole(f, x, ...) ([&](decltype((x)) whole) { return (f)(begin(whole), end(whole), ## __VA_ARGS__); })(x)
using namespace std;
template <class T> inline void setmax(T & a, T const & b) { a = max(a, b); }
template <class T> inline void setmin(T & a, T const & b) { a = min(a, b); }

int main() {
    // input
    int n, k; scanf("%d%d", &n, &k);
    int sqrt_n = ceil(sqrt(n));
    vector<shared_ptr<vector<int> > > a(sqrt_n);
    repeat (b, a.size()) {
        a[b] = make_shared<vector<int> >();
    }
    repeat (i, n) {
        int b = i / a.size();
        int a_i; scanf("%d", &a_i);
        a[b]->push_back(a_i);
    }
    // init
    repeat (b, a.size()) {
        if (a[b]->empty()) a[b] = nullptr;
    }
    a.erase(whole(remove, a, nullptr), a.end());
    vector<int> bucket_min(a.size());
    vector<int> bucket_max(a.size());
    repeat (b, a.size()) {
        bucket_min[b] = *whole(min_element, *a[b]);
        bucket_max[b] = *whole(max_element, *a[b]);
    }
    // insertion sort
    auto find_in_the_same_bucket = [&](int b, int i) {
        auto & a_b = *a[b];
        int jl = i;
        int jr = i;
        while (jl - 1 >= 0 and abs(a_b[jl - 1] - a_b[i]) <= k) {
            -- jl;
            if (a_b[i] <= a_b[jl]) jr = jl;
        }
        return make_pair(jl, jr);
    };
    auto move_in_the_same_bucket = [&](int b, int l, int r) {
        auto & a_b = *a[b];
        int a_l = a_b[r];
        for (; r > l; -- r) {
            a_b[r] = a_b[r - 1];
        }
        a_b[l] = a_l;
    };
    auto increment_in_the_same_bucket = [&](int b, int i) {
        ++ i;
        if (i == a[b]->size()) {
            ++ b;
            i = 0;
        }
        return make_pair(b, i);
    };
    for (int b = 0, i = 0; b < a.size(); ) {
        int jl, jr; tie(jl, jr) = find_in_the_same_bucket(b, i);
        if (jl != 0) {
            move_in_the_same_bucket(b, jr, i);
            tie(b, i) = increment_in_the_same_bucket(b, i);
        } else {
            auto & a_b = *a[b];
            // find
            int cl = b;
            int cr = b;
            while (cl - 1 >= 0 and abs(bucket_min[cl - 1] - a_b[i]) <= k and abs(bucket_max[cl - 1] - a_b[i]) <= k) {
                -- cl;
                if (a_b[i] <= bucket_max[cl]) cr = cl;
            }
            if (cl - 1 >= 0) {
                a[cl - 1]->push_back(a_b[i]);
                int kl, kr; tie(kl, kr) = find_in_the_same_bucket(cl - 1, a[cl - 1]->size() - 1);
                a[cl - 1]->pop_back();
                assert (kl != 0);
                if (kr < a[cl - 1]->size()) {
                    cl = cr = cl - 1;
                }
            }
            // exec
            if (cr == b) {
                move_in_the_same_bucket(b, jr, i);
                tie(b, i) = increment_in_the_same_bucket(b, i);
            } else {
                // remove from a[b]
                int a_i = a_b[i];
                a_b.erase(a_b.begin() + i);
                if (a_b.empty()) {
                    a.erase(a.begin() + b);
                    bucket_min.erase(bucket_min.begin() + b);
                    bucket_max.erase(bucket_max.begin() + b);
                    -- b;
                    assert (i == 0);
                } else {
                    if (bucket_min[b] == a_i) bucket_min[b] = *whole(min_element, a_b);
                    if (bucket_max[b] == a_i) bucket_max[b] = *whole(max_element, a_b);
                }
                // add to a[cr]
                a[cr]->push_back(a_i);
                setmin(bucket_min[cr], a_i);
                setmax(bucket_max[cr], a_i);
                tie(jl, jr) = find_in_the_same_bucket(cr, a[cr]->size() - 1);
                move_in_the_same_bucket(cr, jr, a[cr]->size() - 1);
                if (a[cr]->size() >= 2 * sqrt_n) {
                    int m = a[cr]->size() / 2;
                    auto a_cr1 = make_shared<vector<int> >(a[cr]->begin() + m, a[cr]->end());
                    a[cr]->resize(m);
                    bucket_min[cr] = *whole(min_element, *a[cr]);
                    bucket_max[cr] = *whole(max_element, *a[cr]);
                    a.insert(a.begin() + (cr + 1), a_cr1);
                    bucket_min.insert(bucket_min.begin() + (cr + 1), *whole(min_element, *a_cr1));
                    bucket_max.insert(bucket_max.begin() + (cr + 1), *whole(max_element, *a_cr1));
                    ++ b;
                }
                // increment
                tie(b, i) = increment_in_the_same_bucket(b, i - 1);
            }
        }
    }
    repeat (b, a.size()) {
        repeat (i, a[b]->size()) {
            printf("%d%c", (*a[b])[i], b + 1 == a.size() and i + 1 == a[b]->size() ? '\n' : ' ');
        }
    }
    return 0;
}
```
