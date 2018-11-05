---
title: Educational Codeforces Round 9 F. Magic Matrix（离线暴力、bitset）
date: 2016-03-07 00:27:42
categories: 
- 思维
- 离线思想
tags: 
- bitset
toc: 
---

题意:
>$给你一个n*n, n\le 2500的矩阵，判断这个矩阵是不是魔力矩阵$
>$魔力矩阵的定义为：$
>$1.对角线都为0$
>$2.矩阵对称, 即a\_{ij}=a\_{ji}$
>$3.对于任意一个格子(i,j)满足,\forall k，a[i][j]\le max(a[i][k],a[j][k])$

<!--more-->

分析:
>$bitset[i]:=维护i行比当前这个数小的列的状态$
>$把矩阵所有元素对于值进行排序, 然后对于当前这个a_{ij}询问, 添加进去所有比它小的值到bitset中$
>$回答询问的时候只要看, bitset[i]和bitset[j]中有没有交集, 即存在一个k使得max(a[i][k],a[j][k])<a[i][j]$
>$对于1,2条件就很简单了$
>$时间复杂度O(n^3/64),其实是水过了$

代码:
```cpp
//
//  Created by TaoSama on 2016-03-07
//  Copyright (c) 2016 TaoSama. All rights reserved.
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
#include <bitset>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 2500, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, a[N][N];
bitset<N> b[N];
pair<int, pair<int, int> > q[N * N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d", &n);
    for(int i = 0; i < n; ++i) {
        for(int j = 0; j < n; ++j) {
            scanf("%d", a[i] + j);
            q[i * n + j] = {a[i][j], {i, j}};
        }
    }
    sort(q, q + n * n);
    for(int i = 0; i < n; ++i)
        for(int j = 0; j < n; ++j)
            if(i == j && a[i][j] || a[i][j] != a[j][i])
                return puts("NOT MAGIC");

    int idx = 0;
    for(int i = 0; i < n * n; ++i) {
        auto wh = q[i].second;
        int x = wh.first, y = wh.second;
        while(q[idx].first < a[x][y]) {
            wh = q[idx].second;
            b[wh.first][wh.second] = 1;
            ++idx;
        }
        if((b[x] & b[y]).any()) return puts("NOT MAGIC");
    }
    puts("MAGIC");
    return 0;
}

```