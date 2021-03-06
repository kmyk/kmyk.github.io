---
layout: post
redirect_from:
  - /writeup/algo/etc/poj-3709/
  - /blog/2016/04/23/poj-3709/
date: 2016-04-23T17:53:16+09:00
tags: [ "competitive", "writeup", "poj", "pku", "convex-hull-trick" ]
"target_url": [ "http://poj.org/problem?id=3709" ]
---

# PKU JudgeOnline 3709. K-Anonymous Sequence

## solution

DP + convex hull trick. $O(N)$.


Think the simple $O(N^2)$ DP: $\rm{dp}\_i = \text{the minimum cost to satisfy the condition on } [0,i)$ (if impossible, $\infty$).
The updation is $\rm{dp}\_r = \min \\{ \rm{dp}\_l + \Sigma\_{i \in [l,r)} (a_i - a_l) \mid r - l \ge k \\}$.

From the updation rvalue, 
you can think the lines $l_i: y(x) = (- a_i) \cdot x + (\rm{dp}\_j - \Sigma\_{j \lt i} a_j + j a_j)$,
and you can say $\rm{dp}\_r = \Sigma\_{j \lt r} a_j + \min \\{ f_i(r) \mid i \lt r \\}$.
Using the convex full trick, this becomes $O(N)$.

read your 蟻本 for the detail.

### memo

Fix 3 lines $y_1(x) = a_1 x + b_1$, $y_2(x) = a_2 x + b_2$ and $y_3(x) = a_3 x + b_3$ with $a_1 \le a_2 \le a_3$.
$forall x, \min \\{ y_1(x), y_3(x) \\} \le y_2(x)$ is equivalent that: let $x\_\star$ is the $x$ which $y_1$ and $y_2$ intersects, $y_1(x\_\star) \le y_2(x\_\star)$.
So, $x\_\star = \frac{b_3 - b_1}{a_1 - a_3}$,
$a_1 \cdot \frac{b_3 - b_1}{a_1 - a_3} + b_1 \le a_2 \cdot \frac{b_3 - b_1}{a_1 - a_3} + b_2$,
$(a_1 - a_2)(b_3 - b_1) \le (b_2 - b_1)(a_1 - a_3)$.

## implementation

``` c++
#include <cstdio>
#include <vector>
#include <deque>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
typedef long long ll;
using namespace std;
const ll inf = 1e18+9;
struct line_t { ll a, b; };
ll apply_line(line_t f, ll x) {
    return f.a * x + f.b;
}
ll is_convex(line_t p, line_t q, line_t r) {
    return (q.a - p.a) * (r.b - q.b) < (q.b - p.b) * (r.a - q.a);
}
int main() {
    int cases; scanf("%d", &cases);
    while (cases --) {
        int n, k; scanf("%d%d", &n, &k);
        vector<int> a(n); repeat (i,n) scanf("%d", &a[i]);
        vector<ll> acc(n+1); repeat (i,n) acc[i+1] = acc[i] + a[i];
        deque<line_t> lines;
        vector<ll> dp(n+1, inf);
        dp[0] = 0;
        repeat (i,n) {
            if (i+1 >= k) {
                int j = i+1-k;
                if (dp[j] != inf) {
                    line_t l = { - a[j], dp[j] - acc[j] + j *(ll) a[j] };
                    while (lines.size() >= 2
                            and not is_convex(lines[lines.size()-2], lines[lines.size()-1], l)) {
                        lines.pop_back();
                    }
                    lines.push_back(l);
                }
            }
            while (lines.size() >= 2
                    and apply_line(lines[0], i+1) >= apply_line(lines[1], i+1)) {
                lines.pop_front();
            }
            if (not lines.empty()) {
                dp[i+1] = acc[i+1] + apply_line(lines[0], i+1);
            }
        }
        printf("%lld\n", dp[n]);
    }
    return 0;
}
```
