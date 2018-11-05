---
title: SOJ 4481 学院杯辩论赛（概率dp）
categories:
  - 动态规划
  - 概率/期望dp
  - 
tags:
  - 概率dp
  - 
date: 2016-04-11 01:24:10
toc: 
---
题意：
>$N\le 7，给定2^N个队伍进行N轮比赛$
$每轮比赛将所遇剩余队伍编号排序，第一小的队伍和第二小的比，第三小的和第四小的比，以此类推$
$N轮比赛后剩余一支队伍，问哪只队伍获胜概率最大，输出编号$

<!-- more -->

分析：
>$概率dp，f[i][j]:=队伍j活到第i轮比赛的概率$
$我们发现转移其实类似于线段树，自底向上转移，从第0轮开始$
$对于上一个区间，显然它的左儿子的队友都是右儿子，反之同理$
$时间复杂度O(n\times 2^n)$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-09
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

int n;
double a[1 << 7][1 << 7], f[7][1 << 7];

void dfs(int n, int l, int r) { // [l, r]
    if(l == r) {
        f[n][l] = 1;
        return;
    }
    int m = l + r >> 1;
    dfs(n - 1, l, m);
    dfs(n - 1, m + 1, r);
    for(int i = l; i <= r; ++i) f[n][i] = 0;
    for(int i = l; i <= m; ++i)
        for(int j = m + 1; j <= r; ++j)
            f[n][i] += f[n - 1][i] * f[n - 1][j] * a[i][j];
    for(int i = m + 1; i <= r; ++i)
        for(int j = l; j <= m; ++j)
            f[n][i] += f[n - 1][i] * f[n - 1][j] * a[i][j];
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d", &n) == 1) {
        for(int i = 0; i < 1 << n; ++i)
            for(int j = 0; j < 1 << n; ++j)
                scanf("%lf", a[i] + j);

        dfs(n, 0, (1 << n) - 1);

        pair<double, int> ans = make_pair(-1, -INF);
        for(int i = 0; i < 1 << n; ++i)
            ans = max(ans, make_pair(f[n][i], -i));
        printf("%d\n", -ans.second + 1);
    }
    return 0;
}

```