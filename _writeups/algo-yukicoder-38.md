---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/38/
  - /blog/2016/09/29/yuki-38/
date: "2016-09-29T23:04:44+09:00"
tags: [ "competitive", "writeup", "yukicoder", "exhaustive-search" ]
"target_url": [ "http://yukicoder.me/problems/31" ]
---

# Yukicoder No.38 赤青白ブロック

間に合いそうだし他に思い付かないしで書いたら通ったけど何で間に合うのかなと思ったら、白色は削除しないので($2^{30}$でなくて)$2^{20}$通りだけだからだった。

## solution

赤色か青色のみを削除するので、$2^{20} \approx 10^6$通りの選択肢がある。
全部試して間に合う。

## implementation

``` c++
#include <iostream>
#include <deque>
#include <functional>
#define repeat(i,n) for (int i = 0; (i) < (n); ++(i))
using namespace std;
template <class T> void setmax(T & a, T const & b) { if (a < b) a = b; }
int main() {
    int kr, kb; string s; cin >> kr >> kb >> s;
    deque<char> stk;
    int ans = 0;
    function<int (int)> go = [&](int i) {
        if (stk.size() + s.length() - i <= ans) {
            return 0;
        }
        int acc = 0;
        if (i == s.length()) {
            setmax<int>(acc, stk.size());
        } else {
            if (s[i] == 'R') {
                if (kr-1 >= stk.size() or stk[kr-1] != 'R') {
                    stk.push_front('R');
                    setmax(acc, go(i+1));
                    stk.pop_front();
                }
                setmax(acc, go(i+1));
            } else if (s[i] == 'B') {
                if (kb-1 >= stk.size() or stk[kb-1] != 'B') {
                    stk.push_front('B');
                    setmax(acc, go(i+1));
                    stk.pop_front();
                }
                setmax(acc, go(i+1));
            } else {
                stk.push_front('W');
                setmax(acc, go(i+1));
                stk.pop_front();
            }
        }
        setmax(ans, acc);
        return acc;
    };
    go(0);
    cout << ans << endl;
    return 0;
}
```
