---
title: HDU 3585 maximum shortest distance（二分、最大团）
categories:
  - 图论
  - 最大团
  - 
tags:
  - 最大团
  - 
date: 2016-04-29 21:23:10
toc: 
---
题意：
>$给定N\le 50个点的坐标，从中选出2\le k\le n个点，使得两两最近的距离最远$
$求这个距离$
<!-- more -->

分析：
>$看到最大化最小值就知道二分了$
$二分这个最小值，凡是大于的都连边，然后判断最大团是不是\ge k$
$100次T了，50次就过了$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-29
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
const int N = 60 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

// 1. 最大团点的数量 = 补图中最大独立集点的数量
// 2. 图的染色问题中，最少需要的颜色的数量 = 最大团点的数量
struct MaxClique {
    static const int V = 60;
    bool g[V][V];
    int n, ans, max[V], adj[V][V];
    int path[V], clique[V]; //for record
    //max[i]:= [i, n]'s maximum clique
    //adj[dep][i]:= available vertices

    void init(int _n) { n = _n; }

    bool dfs(int cur, int dep) {
        if(cur == 0) {
            if(dep > ans) {
                ans = dep;
                swap(clique, path);
                return 1;
            }
            return 0;
        }
        for(int i = 0; i < cur; ++i) {
            if(dep + cur - i <= ans) return 0; //dep + left <= ans
            int u = adj[dep][i], nxt = 0;
            if(dep + max[u] <= ans) return 0; //same as above
            path[dep] = u;
            for(int j = i + 1; j < cur; ++j) {
                int v = adj[dep][j];
                if(g[u][v]) adj[dep + 1][nxt++] = v;
            }
            if(dfs(nxt, dep + 1)) return 1;
        }
        return 0;
    }
    int maxClique() {
        ans = 0;
        memset(max, 0, sizeof max);
        for(int i = n - 1; ~i; --i) {
            int cur = 0;
            for(int j = i + 1; j < n; ++j)
                if(g[i][j]) adj[1][cur++] = j;
            path[0] = i;
            dfs(cur, 1);
            max[i] = ans;
        }
        return ans;
    }
} solver;

int n, k;
int x[N], y[N];
double d[N][N];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &k) == 2) {
        solver.init(n);
        for(int i = 0; i < n; ++i) scanf("%d%d", x + i, y + i);
        for(int i = 0; i < n; ++i)
            for(int j = i + 1; j < n; ++j)
                d[i][j] = d[j][i] = hypot(x[i] - x[j], y[i] - y[j]);

        double l = 0, r = 2e4;
        for(int i = 1; i <= 50; ++i) {
            double m = (l + r) / 2;
            for(int u = 0; u < n; ++u)
                for(int v = u + 1; v < n; ++v)
                    solver.g[u][v] = solver.g[v][u] = d[u][v] >= m;
            if(solver.maxClique() >= k) l = m;
            else r = m;
        }
        printf("%.2f\n", l);
    }
    return 0;
}
```