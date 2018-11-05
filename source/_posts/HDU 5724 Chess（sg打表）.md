---
title: HDU 5724 Chess（sg打表）
categories:
  - 数学
  - 博弈
  - 
tags:
  - sg函数
  - 
date: 2016-07-24 18:43:10
toc: 
---

题意：
>$给定一个N\times 20的棋盘，N\le 1000，每行有一些位置有棋子$
$定义一个操作：任意选择1个棋子恰好向右移动1个空位，或者越过连续的一些棋子到一个空位$
$现在2人轮流操作，且最优操作，问先手输赢情况$

<!-- more -->
分析：
>$首先发现每行可以作为1个独立游戏$
$之后2^{20}并不是很大，暴力打出sg值的表，然后合并一下就做完了$
$时间复杂度O(20\times 2^{20})$


代码:
```cpp
//
//  Created by TaoSama on 2016-07-20
//  Copyright (c) 2016 TaoSama. All rights reserved.
//
#pragma comment(linker, "/STACK:102400000,102400000")
#include <algorithm>
#include <cctype>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <ctime>
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
int sg[1 << 20];

int getSg(int x) {
    if(~sg[x]) return sg[x];
    bool mark[100]; memset(mark, 0, sizeof mark);
    for(int i = 0, j; i < 20; ++i) {
        if(x >> i & 1) {
            for(j = i + 1; (x >> j & 1) && j < 20; ++j);
            if(j == 20) continue;
            int y = x ^ (1 << i) | (1 << j);
            mark[getSg(y)] = 1;
        }
    }
    for(int i = 0; ; ++i) if(!mark[i]) return sg[x] = i;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    memset(sg, -1, sizeof sg);
    for(int i = 0; i < 1 << 20; ++i) getSg(i);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d", &n);
        int ans = 0;
        for(int i = 1; i <= n; ++i) {
            int cur = 0;
            int k; scanf("%d", &k);
            while(k--) {
                int x; scanf("%d", &x);
                cur |= 1 << x - 1;
            }
            ans ^= sg[cur];
        }
        puts(ans ? "YES" : "NO");
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```