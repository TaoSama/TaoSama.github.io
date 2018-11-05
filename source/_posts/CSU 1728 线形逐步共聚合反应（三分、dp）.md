---
title: CSU 1728 线形逐步共聚合反应（三分、dp）
categories:
  - 暴力
  - 搜索
  - 二/三分搜索
  - 
tags:
  - 三分搜索
  - 
date: 2016-04-29 00:28:10
toc: 
---
题意：
>$给定N\le 2\times 10^5个数，现要使|\sum_{i=l}^r (A_i - x)|的最大值最小$
<!-- more -->

分析：
>$令s(l,\ r)=\sum_{i=l}^r (A_i - x)$
$max\{|s(l,\ r)|\}$
$=max\{max\left(s(l,\ r),\ -s(l,\ r)\right)\}$
$=max\{max\{s(l,\ r)\},\ max\{-s(l,\ r)\}\}$
$=max\{A,\ B\}$
$我们发现A和B分别是最大和最小连续子段和$
$A随x单调减，B随x单调增，则原函数是单峰的，我们可以用过三分来求这个最小值$
$时间复杂度为O(nlogC)$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-28
//  Copyright (c) 2016 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:102400000,102400000")
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
const int N = 2e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n;
double a[N];

//minimize the maximum substring sum
double check(double x) {
    double minv, maxv, sum1, sum2;
    minv = maxv = sum1 = sum2 = 0;
    for(int i = 1; i <= n; ++i) {
        sum1 += a[i] - x;
        sum2 += a[i] - x;
        minv = min(minv, sum1);
        maxv = max(maxv, sum2);
        if(sum1 > 0) sum1 = 0;
        if(sum2 < 0) sum2 = 0;
    }
    return max(maxv, -minv);
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        for(int i = 1; i <= n; ++i) scanf("%lf", a + i);
        double l = 0, r = 1e4;
        for(int i = 1; i <= 100; ++i) {
            double ll = (2 * l + r) / 3;
            double rr = (l + 2 * r) / 3;
            if(check(ll) < check(rr)) r = rr;
            else l = ll;
        }
        printf("%.12f\n", l);
    }
    return 0;
}
```