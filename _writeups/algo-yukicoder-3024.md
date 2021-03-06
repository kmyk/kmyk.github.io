---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/3024/
  - /blog/2017/04/01/yuki-3024/
date: "2017-04-01T02:29:38+09:00"
tags: [ "competitive", "writeup", "yukicoder", "optimization" ]
"target_url": [ "http://yukicoder.me/problems/no/3024" ]
---

# Yukicoder No.3024 等式

普通に難しめの実装問題。

>   この問題の高速なsolverがほしかったので作問しました

ということなので定数倍高速化する遊びをした。と言っても並列化が難しいのでほぼ変わらずで、元にしたFF256grhyさんの方針が速いだけという感じ。
最初に私が書いた方針はTLEするぐらい遅かったので破棄。

-   `next_permutation`や`set`で頑張るより、配列上でinplaceに探索する方が速いっぽい
-   この制約ならoverflowしないので約分は不要なのでgcdの分速くできる

## implementation

``` c++
#pragma GCC optimize "O3"
#pragma GCC target "avx"
#include <cstdio>
#include <vector>
#include <array>
#include <functional>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
using ll = long long;
using namespace std;

struct rational_t {
    ll p, q; // q may be 0
    bool operator == (rational_t other) const { return this->p * other.q == this->q * other.p; }
    rational_t operator + (rational_t other) const { return { this->p * other.q + other.p * this->q, this->q * other.q }; }
    rational_t operator - (rational_t other) const { return { this->p * other.q - other.p * this->q, this->q * other.q }; }
    rational_t operator * (rational_t other) const { return { this->p * other.p, this->q * other.q }; }
    rational_t operator / (rational_t other) const { return { this->p * other.q, this->q * other.p }; }
};
rational_t make_rational(ll p, ll q) { return { p, q }; }

constexpr int max_n = 7;
struct loop_exception {};
array<rational_t, max_n> x;
void go(int n) {
    repeat (j,n) {
        swap(x[j], x[n-1]);
        repeat (i,j) {
            if (x[i] == x[n-1]) {
                throw loop_exception {};
            }
            if (n == 2) continue;
            rational_t saved = x[i];
            x[i] = saved + x[n-1]; go(n-1);
            x[i] = saved - x[n-1]; go(n-1);
            x[i] = saved * x[n-1]; go(n-1);
            if (x[n-1].p) {
                x[i] = saved / x[n-1]; go(n-1);
            }
            x[i] = saved;
        }
        swap(x[j], x[n-1]);
    }
}

int main() {
    int n; scanf("%d", &n);
    vector<int> a(n); repeat (i,n) scanf("%d", &a[i]);
    bool found = false;
    repeat (i, a.size()) x[i] = make_rational(a[i], 1);
    try {
        go(n);
    } catch (loop_exception e) {
        found = true;
    }
    printf("%s\n", found ? "YES" : "NO");
    return 0;
}
```
