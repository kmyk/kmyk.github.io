---
layout: post
alias: "/blog/2016/01/30/discovery-2016-qual-a/"
title: "DISCO presents ディスカバリーチャンネル Programming Contest 2016 Qualification A - DISCO presents ディスカバリーチャンネルプログラミングコンテスト 2016"
date: 2016-01-30T23:17:12+09:00
tags: [ "competitive", "writeup", "atcoder", "discovery-channel" ]
---

golfで鍛えたperl力。

## [A - DISCO presents ディスカバリーチャンネルプログラミングコンテスト 2016](https://beta.atcoder.jp/contests/discovery2016-qual/tasks/discovery_2016_qual_a)

``` perl
#!/usr/bin/perl
$n = int(<>);
$s = DiscoPresentsDiscoveryChannelProgrammingContest2016;
$s =~ s/(.{${n}})/\1\n/g;
chomp $s;
print $s,$/;
```