---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/450/
  - /blog/2016/12/05/yuki-450/
date: "2016-12-05T04:26:53+09:00"
tags: [ "competitive", "writeup", "yukicoder" ]
"target_url": [ "http://yukicoder.me/problems/no/450" ]
---

# Yukicoder No.450 ベー君のシャトルラン

何も考えず愚直を書いたらTLE/WAしました。

>   誰かが引っかかってくれるかもしれないと思って作った問題でしたが、再考してみれば当たり前だろ・・・となりました。難しいものですね。引っかかってくれた人に（いらっしゃったら）感謝申し上げます。

はい。

``` python
#!/usr/bin/env python3
vl, vr = map(int, input().split())
d = int(input())
w = int(input())
t = d / (vl + vr)
print(w * t)
```
