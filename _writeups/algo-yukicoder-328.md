---
layout: post
redirect_from:
  - /writeup/algo/yukicoder/328/
  - /blog/2016/06/28/yuki-328/
date: 2016-06-28T23:08:30+09:00
tags: [ "competitive", "writeup", "yukicoder", "math" ]
"target_url": [ "http://yukicoder.me/problems/no/328" ]
---

# Yukicoder No.328 きれいな連立方程式

基本対称式なんて単語、数年ぶりに聞きました。(解けず)

## solution

数学。$O(1)$。


与えられた連立方程式

-   $c_1 = p_1       + p_2$
-   $c_2 = p_1 z_1   + p_2 z_2$
-   $c_3 = p_1 z_1^2 + p_2 z_2^2$
-   $c_4 = p_1 z_1^3 + p_2 z_2^3$

は対称式であるので、基本対称式の多項式で書ける。
$p_i^2z_i^k$の項を作って消すことを考えると、

-   $c_1c_3 - c_2^2  = p_1p_2(z_1 - z_2)^2$
-   $c_1c_4 - c_2c_3 = p_1p_2(z_1 - z_2)^2(z_1 + z_2)$
-   $c_2c_4 - c_3^2  = p_1p_2(z_1 - z_2)^2z_1z_2$

ここで$p_1p_2(z_1 - z_2)^2$を消去し、

-   $\frac{c_1c_4 - c_2c_3}{c_1c_3 - c_2^2} = z_1 + z_2$
-   $\frac{c_2c_4 - c_3^2 }{c_1c_3 - c_2^2} = z_1z_2$

を得る。仮定より$c_1c_3 - c_2^2 \ne 0$なので式になっている。
ここからさらに$z_2$を消去すると、

-   $z_1^2 - \frac{c_1c_4 - c_2c_3}{c_1c_3 - c_2^2}z_1 + \frac{c_2c_4 - c_3^2 }{c_1c_3 - c_2^2} = 0$

二次方程式の解の公式$x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}$より、判別式

-   $D = b^2 - 4ac = (\frac{c_1c_4 - c_2c_3}{c_1c_3 - c_2^2})^2 - 4 \cdot \frac{c_2c_4 - c_3^2 }{c_1c_3 - c_2^2}$

の値が非負なら解$z_1$は実数となる。
分母を払って、

-   $(c_1c_4 - c_2c_3)^2 \ge 4(c_1c_3 - c_2^2)(c_2c_4 - c_3^2)$

が成り立つかどうかを見ればよい。

## implementation

``` python
#!/usr/bin/env python3
c1, c2, c3, c4 = map(int,input().split())
p = (c1*c4 - c2*c3)**2 >= 4*(c1*c3 - c2**2)*(c2*c4 - c3**2)
print('R' if p else 'I')
```
