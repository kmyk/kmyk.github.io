---
layout: post
redirect_from:
  - /writeup/algo/aoj/1268/
  - /blog/2015/10/31/aoj-1268/
date: 2015-10-31T01:06:41+09:00
tags: [ "competitive", "icpc", "aoj", "writeup", "bfs", "exhaustive-search", "dice" ]
---

# AOJ 1268 Cubic Eight-Puzzle

icpcに向けてのチームの練習において解いた。

<!-- more -->

## [Cubic Eight-Puzzle](http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=1268) (ICPC Asia 2006 C) {#c}

### 問題

色の塗られたサイコロが8つ、$3\times 3$の盤面上に並んでいる。目標となる状態が与えられるので、8パズルの要領で転がして移動させ、上から見たときの模様が目標と一致するようにせよ。そのための操作の回数の最小値を求めよ。ただしその値が30を越える場合は-1と答えよ。

### 解法

盤面の状態数が$9\cdot 6^8 \sim 1.5\times 10^7$とそう大きくないため、基本方針は全探索でよい。
最小の手数を求めるので、単に幅優先探索を行なう。
単に実装するとMLE/TLEすることがある。
30手で探索を打ち切ることを用いて、目標盤面との不一致の数で枝刈りすれば通る。

反復深化深さ優先探索でも可能であるようだ。

### 実装

``` c++
if (toupper(f[y][x] != toupper(g[y][x]))) {
```

とやってバグらせた。`-Wall`も`cppcheck`も教えてくれなかった。

``` c++
#include <iostream>
#include <unordered_set>
#include <queue>
#include <algorithm>
#define repeat_from(i,m,n) for (int i = (m); (i) < (n); ++(i))
#define repeat(i,n) repeat_from(i,0,n)
using namespace std;

// Wn R   Wr B
//   BWB    RWR
//    R      B
// Bn R   Br W
//   WBW    RBR
//    R      W
// Rn W   Rr B
//   BRB    WRW
//    W      B
const char Wn = 'W';
const char Wr = 'w';
const char Bn = 'B';
const char Br = 'b';
const char Rn = 'R';
const char Rr = 'r';
const char E  = 'E';
char next_tate(char dice) {
    return
        dice == Wn ? Rn :
        dice == Wr ? Br :
        dice == Bn ? Rr :
        dice == Br ? Wr :
        dice == Rn ? Wn :
        dice == Rr ? Bn : 0;
}
char next_yoko(char dice) {
    return
        dice == Wn ? Bn :
        dice == Wr ? Rr :
        dice == Bn ? Wn :
        dice == Br ? Rn :
        dice == Rn ? Br :
        dice == Rr ? Wr : 0;
}
struct state_t {
    char f[3][3];
    int8_t ey, ex;
    int8_t n;
    int8_t match;
};
bool equals_top(char const (& f)[3][3], char const (& g)[3][3]) {
    repeat (y,3) repeat (x,3) {
        if (toupper(f[y][x]) != toupper(g[y][x])) {
            return false;
        }
    }
    return true;
}
const int dy[] = { -1, 1, 0, 0 };
const int dx[] = { 0, 0, -1, 1 };
uint32_t pack(char const (& f)[3][3]) {
    uint32_t z = 0;
    repeat (y,3) {
        repeat (x,3) {
            z *= 7;
            z +=
                f[y][x] == Wn ? 1 :
                f[y][x] == Wr ? 2 :
                f[y][x] == Bn ? 3 :
                f[y][x] == Br ? 4 :
                f[y][x] == Rn ? 5 :
                f[y][x] == Rr ? 6 : 0;
        }
    }
    return z;
}

int main() {
    while (true) {
        int sex, sey; cin >> sex >> sey;
        if (sex == 0 and sey == 0) break;
        -- sex; -- sey;
        char goal[3][3];
        repeat (y,3) repeat (x,3) cin >> goal[y][x];
        unordered_set<uint32_t> used;
        queue<state_t> que; {
            state_t s;
            repeat (y,3) repeat (x,3) s.f[y][x] = Wn;
            s.f[sey][sex] = E;
            s.ey = sey;
            s.ex = sex;
            s.n = 0;
            s.match = 0;
            repeat (y,3) repeat (x,3) if (goal[y][x] == s.f[y][x]) s.match += 1;
            que.push(s);
            used.insert(pack(s.f));
        }
        int result = -1;
        while (not que.empty()) {
            state_t s = que.front(); que.pop();
//repeat (y,3) {
//repeat (x,3) {
//cerr << s.f[y][x];
//}
//cerr << endl;
//}
//cerr << endl;
            if (equals_top(s.f, goal)) {
                result = s.n;
                break;
            }
            if (s.n == 30) continue;
            if (9 - s.match > 30 - s.n + 1) continue;
            repeat (i,4) {
                state_t t = s;
                t.ey = s.ey + dy[i];
                t.ex = s.ex + dx[i];
                if (t.ey < 0 or 3 <= t.ey or t.ex < 0 or 3 <= t.ex) continue;
                if (toupper(t.f[s.ey][s.ex]) == goal[s.ey][s.ex]) t.match -= 1;
                if (toupper(t.f[t.ey][t.ex]) == goal[t.ey][t.ex]) t.match -= 1;
                t.f[s.ey][s.ex] = dx[i] ? next_yoko(t.f[t.ey][t.ex]) : next_tate(t.f[t.ey][t.ex]);
                t.f[t.ey][t.ex] = E;
                if (toupper(t.f[s.ey][s.ex]) == goal[s.ey][s.ex]) t.match += 1;
                if (toupper(t.f[t.ey][t.ex]) == goal[t.ey][t.ex]) t.match += 1;
                t.n += 1;
                if (used.count(pack(t.f))) continue;
                que.push(t);
                used.insert(pack(t.f));
            }
        }
        cout << result << endl;
    }
    return 0;
}
```
