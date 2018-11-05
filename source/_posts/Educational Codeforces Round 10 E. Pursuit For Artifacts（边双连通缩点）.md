---
title: Educational Codeforces Round 10 E. Pursuit For Artifacts（边双连通缩点）
categories:
  - 图论
  - 连通图
  - 
tags:
  - 边双连通
  - 
date: 2016-03-28 22:00:10
toc: 
---
题意：
>$N，M\le 3\times 10^5，N个点，M条边的无向图，无重边自环$
$边权为0或1，问s\to t是否存权\ge 1且每条边经过一次的一条路径$

<!-- more -->

分析：
>$边双缩点成树，树边(桥边)权保留，bcc内总权映射到点上$
$ans=dis(s,t)\ge 1$
$正确性就是bcc任意2点连通，是个简单环嘛，选有1的那半边走就好啦$ $并且树上2点路径是唯一的，满足题意就做完了$
$时间复杂度O(n+m)$

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
const int N = 3e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
struct Edge {
    int v, c;
};
vector<Edge> G[N], T[N];
int dfn[N], low[N], in[N], id[N], bcc, dfsNum;
int stk[N], top;

void tarjan(int u, int f) {
    dfn[u] = low[u] = ++dfsNum;
    stk[++top] = u;
    in[u] = true;
    for(Edge e : G[u]) {
        int v = e.v;
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
            in[v] = false;
            id[v] = bcc;
            if(v == u) break;
        }
    }
}

void init() {
    bcc = dfsNum = 0;
    memset(dfn, 0, sizeof dfn);
    tarjan(1, -1);
}

int val[N];
bool dfs(int s, int t, int fa, int sum) {
    if(s == t) return sum + val[t];
    for(Edge e : T[s]) {
        int v = e.v;
        if(v == fa) continue;
        if(dfs(v, t, s, sum + e.c + val[v])) return true;
    }
    return false;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    scanf("%d%d", &n, &m);
    for(int i = 1; i <= m; ++i) {
        int u, v, c; scanf("%d%d%d", &u, &v, &c);
        G[u].push_back({v, c});
        G[v].push_back({u, c});
    }

    init();

    for(int i = 1; i <= n; ++i) {
        int u = id[i];
        for(Edge e : G[i]) {
            int v = id[e.v];
            if(u == v) val[u] += e.c;
            else T[u].push_back({v, e.c});
        }
    }
    int s, t; scanf("%d%d", &s, &t);
    puts(dfs(id[s], id[t], -1, val[id[s]]) ? "YES" : "NO");
    return 0;
}
```