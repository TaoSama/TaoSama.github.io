---
title: ZOJ 3939 The Lucky Week（打表）
categories:
  - 暴力
  - 
  - 
tags:
  - 
  - 
date: 2016-04-25 22:36:10
toc: 
---
题意：
>$Lucky\ Week:某个星期一是这月的1、11、21号的话$
$给定第1个Lucky\ Week的星期一，问第N\le 10^9的日期$

<!-- more -->

分析：
>$写个函数来打表咯，发现400就是周期了，然后就很简单了$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-23
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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int Y, M, D, n;

bool isLeap(int y) {
    return y % 4 == 0 && y % 100 || y % 400 == 0;
}

int mon[] = {0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};

bool go(int& y, int& m, int& d) {
    d += 7;
    int curMonth = m == 2 && isLeap(y) ? mon[2] + 1 : mon[m];
    if(d > curMonth) ++m, d -= curMonth;
    if(m > 12) ++y, m -= 12;
    return d == 1 || d == 11 || d == 21;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    Y = 2016, M = 4, D = 11;
    int y = 2016, m = 4, d = 11;
    int cnt = 1;
    while(true) {  //2058
        bool have = go(y, m, d);
        cnt += have;
        if(make_tuple(y, m, d) >= make_tuple(Y + 400, M, D)) {
            cnt -= have;
            break;
        }
    }
//    printf("!!%d %d %d\n", y, m, d);
//    prln(cnt);


    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d%d%d", &Y, &M, &D, &n); --n;
        Y += 400 * (n / 2058);
        n %= 2058;
        for(int i = 1; i <= n;)
            if(go(Y, M, D)) ++i;
        printf("%d %d %d\n", Y, M, D);
    }
    return 0;
}

```