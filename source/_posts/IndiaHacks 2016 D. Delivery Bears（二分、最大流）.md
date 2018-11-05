---
title: IndiaHacks 2016 D. Delivery Bears（二分、最大流）
categories:
  - 图论
  - 网络流
  - 
tags:
  - 二分搜索
  - 最大流
date: 2016-03-21 21:50:10
toc:
---
题意：
>$给定N\le 50个城市，M\le 500条有向边，X\le10^5为熊的个数$
$边描述为(u_i,v_i,c_i)，表示u_i\to v_i可以通过c_i物品$
$现要求恰好用X只熊，且每只熊运送的物品多少相同$
$求最多能从1到n运多少物品$

<!-- more -->
分析：
>$首先很显然要二分每只熊运送多少物品，答案就是X*这个值$
$接下来如何check可行，显然可以转换一下模型$
$把每条边运送物品变为在当前的mid物品下，可以通过多少只熊$
$所以check就变成了，1到n能否至少通过X只熊，建图后判断最大流是不是\ge X$
$需要注意的是，容量可能会爆int$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-19
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
const int N = 50 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const int M = 1000 + 10;

int head[N], pnt[M], cap[M], nxt[M], cnt;

void add_edge(int u, int v, int w) {
    pnt[cnt] = v;
    cap[cnt] = w;
    nxt[cnt] = head[u];
    head[u] = cnt++;
}

void add_double(int u, int v, int w1, int w2 = 0) {
    add_edge(u, v, w1);
    add_edge(v, u, w2);
}

int lev[N], cur[N];
bool bfs(int s, int t) {
    queue<int> q;
    memset(lev, 0, sizeof lev);
    q.push(s);  lev[s] = 1;
    while(q.size() && !lev[t]) {
        int u = q.front(); q.pop();
        for(int i = head[u]; ~i; i = nxt[i]) {
            int v = pnt[i];
            if(cap[i] > 0 && !lev[v]) {
                lev[v] = lev[u] + 1;
                q.push(v);
            }
        }
    }
    return lev[t];
}

int dfs(int u, int t, int delta) {
    if(u == t || !delta) return delta;
    int ret = 0;
    for(int i = cur[u]; ~i; i = nxt[i]) {
        int v = pnt[i];
        if(cap[i] > 0 && lev[v] == lev[u] + 1) {
            int d = dfs(v, t, min(delta, cap[i]));
            cur[u] = i;
            ret += d; delta -= d;
            cap[i] -= d;
            cap[i ^ 1] += d;

            if(delta == 0) return ret;
        }
    }
    lev[u] = 0;
    return ret;
}

int dinic(int s, int t) {
    int ret = 0;
    while(bfs(s, t)) {
        for(int i = s; i <= t; ++i) cur[i] = head[i];
        ret += dfs(s, t, INF);
    }
    return ret;
}

int n, m, k;
int u[M], v[M], c[M];

bool check(double x) {
    cnt = 0; memset(head, -1, sizeof head);
    for(int i = 1; i <= m; ++i)
        add_double(u[i], v[i], min(c[i] / x, 1e9));
    return dinic(1, n) >= k;
}


int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d%d%d", &n, &m, &k);
    for(int i = 1; i <= m; ++i) scanf("%d%d%d", u + i, v + i, c + i);
    double l = 0, r = 1e6;
    for(int i = 1; i <= 100; ++i) {
        double mid = (l + r) / 2;
        if(check(mid)) l = mid;
        else r = mid;
    }
    printf("%.12f\n", l * k);
    return 0;
}

```