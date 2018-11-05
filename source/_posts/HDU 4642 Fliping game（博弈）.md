---
title: HDU 4642 Fliping game（博弈）
categories:
  - 数学
  - 博弈
  - 
tags:
  - 博弈
  - 
date: 2016-03-26 02:40:10
toc: 
---
题意：
>$N,\ M\le 100，N\times M的棋盘，每个值为0或者1$
$A和B玩游戏，每次选择一个矩形区域把里面的01翻转，但要求选择的左上角必须为1$
$谁不能操作了谁输，假设2个人采取最优策略，输出胜者$

<!-- more -->

分析：
>$可以看出，每次翻转都会翻动最右下角的格子$
$如果右下角刚开始为1，那么先手的人每次都翻动右下角的，使该格子变为0$
$后手的翻其他矩形，肯定会使得最右下角的格子变为1$
$这样先手每次都翻右下角这个格子，最后肯定是后手败$
$如果右下角刚开始为0，同理后手可以通过这个策略来使自己必胜$
$所以A_{nm}为1先手胜，否则后手胜$
$时间复杂度为O(nm)$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-25
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

int n, m;
int a[105][105];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        for(int i = 1; i <= n; ++i)
            for(int j = 1; j <= m; ++j)
                scanf("%d", a[i] + j);
        puts(a[n][m] ? "Alice" : "Bob");
    }
    return 0;
}


```