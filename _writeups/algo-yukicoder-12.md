---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/12/
  - /blog/2017/01/11/yuki-12/
date: "2017-01-11T15:06:27+09:00"
tags: [ "competitive", "writeup", "yukicoder", "shakutori-method" ]
"target_url": [ "http://yukicoder.me/problems/34" ]
---

# Yukicoder No.12 限定された素数

入力は$2^{10}$種類なので埋め込みもできそう。

## solution

しゃくとり法。素数判定が効いて上限$L = 5000000$に対し$O(L \log \log L)$。

区間の右端$r$を固定したとき、禁止された数字を含まないような最小の左端$l = l_r$は一意に定まる。
含まないといけない数字を最も含む左端のもこの$l_r$なので、これだけ考えればよい。
$r \le r'$なら$l_r \le l\_{r'}$なので、しゃくとり法で線形になる。

## implementation

``` c++
#include <iostream>
#include <vector>
#include <array>
#define repeat(i,n) for (int i = 0; (i) < int(n); ++(i))
using ll = long long;
using namespace std;
template <class T> void setmax(T & a, T const & b) { if (a < b) a = b; }
const int limit = 5000000;
vector<bool> sieve_of_eratosthenes(int n) { // enumerate primes in [2,n] with O(n log log n)
    vector<bool> is_prime(n+1, true);
    is_prime[0] = is_prime[1] = false;
    for (int i = 2; i*(ll)i <= n; ++i)
        if (is_prime[i])
            for (int k = i+i; k <= n; k += i)
                is_prime[k] = false;
    return is_prime;
}
int main() {
    vector<bool> is_prime = sieve_of_eratosthenes(limit);
    int n; cin >> n;
    array<bool, 10> a = {};
    repeat (i,n) { int d; cin >> d; a[d] = true; }
    array<int, 10> used = {};
    auto use = [&](int p, int delta) {
        while (p) {
            used[p % 10] += delta;
            p /= 10;
        }
    };
    int ans = -1;
    for (int k = 1, l = 1; l <= limit; ++ l) {
        if (is_prime[l]) use(l, +1);
        repeat (d,10) if (not a[d]) while (used[d]) {
            if (is_prime[k]) use(k, -1);
            ++ k;
        }
        bool is_fulfilled = true;
        repeat (d,10) {
            if (a[d] and not used[d]) {
                is_fulfilled = false;
                break;
            }
        }
        if (is_fulfilled) setmax(ans, l - k);
    }
    cout << ans << endl;
    return 0;
}
```
