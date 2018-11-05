---
title: HDU 4650 Minimum Average Weight Path（floyd）
categories:
  - 图论
  - 最短路
  - 
tags:
  - floyd
  - 
date: 2016-03-28 15:42:10
toc: 
---
题意：
>$N\le 100，M\le 10^4，N个点，M条边的图，无重边，可能有自环$
$所有节点对(u,\ v)的min\{\frac{dis(u,\ v)}{len(u,\ v)}\}，不连通输出NO$

<!-- more -->

分析：
>$floyd求一发连通性，判断NO$
$然后f[k][i][j]:=i到j长度为k的最短路$
$floyd的循环顺序是可以换的嘛，把i拿到最外面去枚举，这样可以复用数组$
$就变成f[k][j]:=i出发，长度为k到j的最短路$
$然后更新答案，注意由于有环，那么判断可不可以经过环$
$经过的话，环的答案如果比较小，显然可以无限绕环来逼近环长（极限思想嘛）$
$时间复杂度O(n^4)$


代码：
```cpp
//
//  Created by TaoSama on 2016-03-12
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

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e2 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
int f[N][N], g[N][N], con[N][N];
double ans[N][N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &m) == 2) {
        memset(g, 0x3f, sizeof g);
        memset(con, 0, sizeof con);
        for(int i = 1; i <= n; ++i)
            for(int j = 1; j <= n; ++j)
                ans[i][j] = 1e18;
        for(int i = 1; i <= m; ++i) {
            int x, y, c; scanf("%d%d%d", &x, &y, &c);
            g[x][y] = c;
            con[x][y] = 1;
        }
        for(int k = 1; k <= n; ++k)
            for(int i = 1; i <= n; ++i)
                for(int j = 1; j <= n; ++j)
                    con[i][j] |= con[i][k] & con[k][j];
        for(int i = 1; i <= n; ++i) {
            memset(f, 0x3f, sizeof f);
            f[0][i] = 0;
            for(int l = 1; l <= n; ++l)
                for(int k = 1; k <= n; ++k)
                    for(int j = 1; j <= n; ++j)
                        f[l][j] = min(f[l][j], f[l - 1][k] + g[k][j]);
            for(int l = 1; l <= n; ++l)
                for(int j = 1; j <= n; ++j)
                    ans[i][j] = min(ans[i][j], f[l][j] * 1. / l);
        }
        for(int i = 1; i <= n; ++i) {
            for(int j = 1; j <= n; ++j) {
                if(!con[i][j]) printf("NO%c", " \n"[j == n]);
                else {
                    for(int k = 1; k <= n; ++k)
                        if(con[i][k] & con[k][j])
                            ans[i][j] = min(ans[i][j], ans[k][k]);
                    printf("%.3f%c", ans[i][j], " \n"[j == n]);
                }
            }
        }
    }
    return 0;
}

```