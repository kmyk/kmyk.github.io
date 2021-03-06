---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/348/
  - /blog/2016/02/26/yuki-348/
date: 2016-02-26T23:45:19+09:00
tags: [ "competitive", "writeup", "yukicoder", "implementation" ]
---

# Yukicoder No.348 カゴメカゴメ

発想はそう難しくはない分実装が少し重め。icpcっぽい。

## [No.348 カゴメカゴメ](http://yukicoder.me/problems/795)

### 解法

やる。

1.  輪を作る。
    -   普通に。
2.  包含関係のグラフを作る。
    -   光線を撃って交わった回数でごにょごにょみたいなのは、線に厚みがあるから無理だろう。
    -   輪の内側か外側かの判定がしたい。そのマスを含む最内の輪がなにかを全マスに関して更新する感じで。外側から埋めていく。
3.  使用する輪を選ぶ。
    -   木になるので普通にやる。その輪を使ったとき/使わなかったときの最高得点を更新。

### 実装

``` c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <set>
#include <functional>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
template <typename T> bool setmax(T & l, T const & r) { if (not (l < r)) return false; l = r; return true; }
using namespace std;
struct point_t { int y, x; };
const int dy[] = { -1, 1, 0, 0,  -1, 1, -1, 1 }; // 4 + 4 neighborhood
const int dx[] = { 0, 0, -1, 1,  -1, -1, 1, 1 };
int main() {
    // input
    int h, w; cin >> h >> w;
    vector<vector<bool> > is_x(h, vector<bool>(w));
    repeat (y,h) repeat (x,w) {
        char c; cin >> c;
        is_x[y][x] = c == 'x';
    }
    auto is_on_field = [&](int y, int x) {
        return 0 <= y and y < h and 0 <= x and x < w;
    };
    // find rings
    vector<vector<point_t> > rings;
    vector<vector<int> > rev(h, vector<int>(w, -1));
    function<void (int, int)> make_ring = [&](int y, int x) {
        if (not is_on_field(y, x)) return;
        if (not is_x[y][x]) return;
        if (rev[y][x] != -1) return;
        rings.back().push_back((point_t) { y, x });
        rev[y][x] = rings.size() - 1;
        repeat (i,8) make_ring(y + dy[i], x + dx[i]);
    };
    repeat (y,h) repeat (x,w) if (is_x[y][x] and rev[y][x] == -1) {
        rings.emplace_back();
        make_ring(y, x);
    }
    int n = rings.size();
    // construct the graph of inclusion
    rings.emplace_back(); // a dummy ring
    vector<set<int> > is_in(n + 1);
    vector<vector<bool> > used(h, vector<bool>(w));
    function<void (int, int, int)> analyze_inclusion = [&](int y, int x, int r) {
        if (not is_on_field(y, x)) return;
        if (used[y][x]) return;
        if (rev[y][x] != -1 and rev[y][x] != r) {
            is_in[r].insert(rev[y][x]);
            return;
        }
        used[y][x] = true;
        repeat (i,4) analyze_inclusion(y + dy[i], x + dx[i], r);
    };
    repeat (y,h) {
        analyze_inclusion(y,   0, n);
        analyze_inclusion(y, w-1, n);
    }
    repeat (x,w) {
        analyze_inclusion(  0, x, n);
        analyze_inclusion(h-1, x, n);
    }
    repeat (y,h) repeat (x,w) if (rev[y][x] != -1 and not used[y][x]) {
        int r = rev[y][x];
        for (point_t p : rings[r]) {
            analyze_inclusion(p.y, p.x, r);
        }
    }
    // calculate score
    vector<vector<int> > memo(2, vector<int>(n + 1));
    function<void (int)> calculate_score = [&](int r) {
        for (int s : is_in[r]) calculate_score(s);
        memo[1][r] += rings[r].size(); // use r
        for (int s : is_in[r]) memo[1][r] += memo[0][s];
        for (int s : is_in[r]) memo[0][r] += memo[1][s]; // don't use r
        setmax(memo[1][r], memo[0][r]);
    };
    calculate_score(n);
    // output
    cout << memo[1][n]<< endl;
    return 0;
}
```
