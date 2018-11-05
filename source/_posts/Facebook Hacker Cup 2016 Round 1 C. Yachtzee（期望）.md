---
title: Facebook Hacker Cup 2016 Round 1 C. Yachtzee（期望）
categories:
  - 数学
  - 概率/期望
  - 
tags:
  - 期望
  - 
date: 2016-04-12 13:56:10
toc: 
---
题意：
>$给定初始金钱[A,\ B]均匀分布，A,\ B\le 10^9$
$现在购买一个东西，分成N\le 10^5部分，每部分价值为C_i\le 10^9$
$会不停的按顺序购买N个部分直到不能购买为止$
$问剩余钱数的期望$

<!-- more -->

分析：
>$根据期望的线性可加性，我们可以求[0,\ B]的期望减去[0,\ A]的期望$
$然后对于[0,\ x]的期望，对C_i求个前缀和，对于每个区间[C_{i-1},\ C_i]$
$L_i=C_i-C_{i-1}，如果完全覆盖那么它的贡献是L_i\cdot\frac{L_i}{2}$
$周期cycle = x/C_n，剩下的left=x\%C_n，计算方式相同$
$那么总贡献E=cycle\cdot L_i\cdot\frac{L_i}{2}+E_{left}，期望E'=\frac{E_B-E_A}{B-A}$

代码：
```cpp
//
//  Created by TaoSama on 2016-01-17
//  Copyright (c) 2015 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:1024000000,1024000000")
#include <algorithm>
#include <cctype>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <iomanip>
#include <iostream>
#include <map>
#include <queue>
#include <string>
#include <set>
#include <vector>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, A, B;
typedef long long LL;
LL a[N];

LL calc(int x) {
    LL ret = 0;
    LL cycle = x / a[n], left = x % a[n];
    for(int i = 1; i <= n; ++i)
        ret += (a[i] - a[i - 1]) * (a[i] - a[i - 1]);
    ret *= cycle;
    int k = lower_bound(a + 1, a + n + 1, left) - a;
    for(int i = 1; i <= k; ++i)
        ret += (min(a[i], left) - a[i - 1]) * (min(a[i], left) - a[i - 1]);
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("yachtzee.txt", "r", stdin);
    freopen("yachtzee_out.txt", "w", stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d%d", &n, &A, &B);
        for(int i = 1; i <= n; ++i) {
            scanf("%lld", a + i);
            a[i] += a[i - 1];
        }
        double ans = 1.0 * (calc(B) - calc(A)) / 2 / (B - A);
        static int kase = 0;
        printf("Case #%d: %.9f\n", ++kase, ans);
    }
    return 0;
}

```