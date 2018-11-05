---
title: HDU 5739 Fantasia（点双连通、树形dp）
categories:
  - 图论
  - 连通图
  - 
tags:
  - 点双连通
  - 树形dp
  - 
date: 2016-07-24 22:56:10
toc: 
---

题意：
>$N\le 10^5个点，M\le 2\times 10^5的无向图$
$定义一个图的权值：图连通就是点权积，不连通就是连通分量的权值和$
$问删去i点后的图G_i的权值$

<!-- more -->
分析：
>![](http://7xru22.com1.z0.glb.clouddn.com/16-7-24/80434423.jpg)
$时间复杂度O(n+m)$


代码:
```cpp
//
//  Created by TaoSama on 2016-07-22
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
const int N = 2e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m, val[N];
vector<int> G[N], T[N];

int dfn[N], low[N], cut[N], bcc, dfsNum;
vector<int> block[N];
int stk[N], top;

void tarjan(int u, int f) {
    dfn[u] = low[u] = ++dfsNum;
    stk[++top] = u;
    int son = 0;
    for(int v : G[u]) {
        if(v == f) continue;
        if(!dfn[v]) {
            ++son;
            tarjan(v, u);
            low[u] = min(low[u], low[v]);
            if(low[v] >= dfn[u]) {
                cut[u] = true;
                block[++bcc].push_back(u);
                while(true) {
                    int x = stk[top--];
                    block[bcc].push_back(x);
                    if(x == v) break;
                }
            }
        } else low[u] = min(low[u], dfn[v]);
    }
    if(f < 0 && son == 1) cut[u] = false;
}

void init() {
    bcc = n;
    dfsNum = 0;
    memset(dfn, 0, sizeof dfn);
    memset(cut, 0, sizeof cut);
}

typedef long long LL;

LL quick(LL x, LL n) {
    LL ret = 1;
    for(; n; n >>= 1) {
        if(n & 1) ret = ret * x % MOD;
        x = x * x % MOD;
    }
    return ret;
}

void add(LL& x, LL y) {
    if(y < 0) y += MOD;
    if((x += y) >= MOD) x -= MOD;
}

bool vis[N];
LL f[N], g[N], sum;
void dfs1(int u) {
    vis[u] = true;
    f[u] = val[u];
    for(int v : T[u]) {
        if(vis[v]) continue;
        dfs1(v);
        f[u] = f[u] * f[v] % MOD;
    }
}

void dfs2(int u, int fa, int rt) {
    vis[u] = true;
    for(int v : T[u]) {
        if(v == fa) continue;
        dfs2(v, u, rt);
    }
    if(u <= n) {
        LL up = f[rt] * quick(f[u], MOD - 2) % MOD, dw = 0;
        for(int v : T[u]) {
            if(v == fa) continue;
            add(dw, f[v]);
        }
//        pr(rt); pr(u); pr(up); prln(dw);
        if(!T[u].size() || u == rt) up = 0;
        g[u] = (sum - f[rt] + up + dw) % MOD;
    }
}

const int C = 2000;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt", "w", stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int kase = 0;

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        if(++kase == C) printf("%d %d\n", n, m);

        for(int i = 1; i <= 2 * n; ++i) {
            val[i] = 1;
            G[i].clear();
            T[i].clear();
            block[i].clear();
        }

        for(int i = 1; i <= n; ++i) {
            scanf("%d", val + i);
            if(kase == C) printf("%d ", val[i]);
        }
        if(kase == C) puts("");
        for(int i = 1; i <= m; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            if(kase == C) printf("%d %d\n", u, v);
            G[u].push_back(v);
            G[v].push_back(u);
        }

        init();
        for(int i = 1; i <= n; ++i) if(!dfn[i]) tarjan(i, -1);

        for(int u = n + 1; u <= bcc; ++u) {
            for(int v : block[u]) {
                T[u].push_back(v);
                T[v].push_back(u);
            }
        }

        sum = 0;
        memset(vis, 0, sizeof vis);
        for(int i = 1; i <= n; ++i) if(!vis[i]) {dfs1(i); add(sum, f[i]);}

        memset(vis, 0, sizeof vis);
        for(int i = 1; i <= n; ++i) if(!vis[i]) dfs2(i, -1, i);

        LL ans = 0;
        for(int i = 1; i <= n; ++i) add(ans, i * g[i] % MOD);
        printf("%I64d\n", ans);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```