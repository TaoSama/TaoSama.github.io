---
title: CodeForces 231E Cactus（边双缩点、LCA）
categories:
  - 图论
  - 连通图
  - 
tags:
  - 边双缩点
  - LCA
  - 
date: 2016-08-09 10:20:10
toc: 
---

题意： 
>$给定一颗N\le 10^5个点的仙人掌，M\le 10^5条边$
$仙人掌定义为：任意一个点至多属于一个简单环$
$Q\le 10^5询问，(u,\ v)有多少条简单路径可达$

<!-- more -->
分析：
>$考虑仙人掌的形态，任意2点之间的路径必然是由许多链和环构成$
$一个环有2种走法，每经过一个环方法数\times 2$
$那么问题转化为任意两点之间有多少简单环$
$那么显然边双缩点，点权为0/1（如果bcc大小>1）$
$之后LCA求2点之间的路径长度就好，记得特判LCA$
$答案ans=2^{环的个数}$

```cpp
//
//  Created by TaoSama on 2016-08-07
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

int n, m;
vector<int> G[N], T[N];

int dfn[N], low[N], sz[N], id[N], bcc, dfsNum;
int stk[N], top;

void tarjan(int u, int f) {
    dfn[u] = low[u] = ++dfsNum;
    stk[++top] = u;
    for(int v : G[u]) {
        if(v == f) continue;
        if(!dfn[v]) {
            tarjan(v, u);
            low[u] = min(low[u], low[v]);
        } else low[u] = min(low[u], dfn[v]);
    }
    if(low[u] == dfn[u]) {
        ++bcc;
        while(true) {
            int v = stk[top--];
            ++sz[bcc];
            id[v] = bcc;
            if(v == u) break;
        }
    }
}

const int LOG = 17;
int dep[N], dis[N], p[LOG][N];

void dfs(int u, int f) {
    p[0][u] = f;
    dis[u] = dis[f] + (sz[u] > 1);
    for(int i = 1; i < LOG; ++i) p[i][u] = p[i - 1][p[i - 1][u]];
    for(int v : T[u]) {
        if(v == f) continue;
        dep[v] = dep[u] + 1;
        dfs(v, u);
    }
}

int lca(int u, int v) {
    if(dep[u] > dep[v]) swap(u, v);
    for(int i = 0; i < LOG; ++i)
        if(dep[v] - dep[u] >> i & 1) v = p[i][v];
    if(u == v) return u;
    for(int i = LOG - 1; ~i; --i)
        if(p[i][u] != p[i][v])
            u = p[i][u], v = p[i][v];
    return p[0][u];
}

int get(int u, int v) {
    int g = lca(u, v);
    return dis[u] + dis[v] - 2 * dis[g] + (sz[g] > 1);
}

void init() {
    bcc = dfsNum = 0;
    memset(dfn, 0, sizeof dfn);
    tarjan(1, -1);
}

int ksm(int x, int n) {
    int ret = 1;
    for(; n; n >>= 1) {
        if(n & 1) ret = 1LL * ret * x % MOD;
        x = 1LL * x * x % MOD;
    }
    return ret;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d%d", &n, &m);
    for(int i = 1; i <= m; ++i) {
        int u, v; scanf("%d%d", &u, &v);
        G[u].push_back(v);
        G[v].push_back(u);
    }

    init();
    for(int i = 1; i <= n; ++i) {
        int u = id[i];
        for(int j : G[i]) {
            int v = id[j];
            if(u == v) continue;
            T[u].push_back(v);
        }
    }

    dfs(1, 0);

    int q; scanf("%d", &q);
    while(q--) {
        int x, y; scanf("%d%d", &x, &y);
        int d = get(id[x], id[y]);
        int ans = ksm(2, d);
        printf("%d\n", ans);
    }

    return 0;
}
```