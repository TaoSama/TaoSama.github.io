---
title: Codeforces Round 346 (Div. 2) F. Polycarp and Hay（逆向思维、bfs）
categories:
  - 思维
  - 逆向思维
  - 
tags:
  - bfs
  - 
date: 2016-04-07 23:40:10
toc: 
---
题意：
>$N，M\le 10^3，N\times M的矩阵，每个格子A_{ij}\le 10^9$
$现选出一些数变小使得它们的和为K\le 10^{18}，需满足：$
$1.至少有1个数不变$
$2.选出的所有数必须相同$
$3.选出的数必须连通$
$存在输出YES，打印任意解，否则输出NO$

<!-- more -->

分析：
>$枚举不变的数，显然它们都是K的约数，先把它们村下来$
$赛上写的从小到大枚举bfs，加了BIT优化，蓝儿并不能改变O((nm)^2)的命运$
$但是如果你想想从大到小的话，就可以复用之前bfs到的了$
$用并查集维护连通的size，大的一定可以变小嘛，这样复杂度就保证了$
$找到答案之后，再重新bfs一次打印解就好了$
$不完全一样的东西少复用啊，强迫症要改啊$
$时间复杂度O(nm)$

代码：
```cpp
//
//  Created by TaoSama on 2016-03-31
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
vector<int> G[N], T[N];
int dfn[N], low[N], in[N], id[N], bcc, dfsNum;
int stk[N], top;

void tarjan(int u, int f) {
    dfn[u] = low[u] = ++dfsNum;
    stk[++top] = u;
    in[u] = true;
    for(int& v : G[u]) {
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
}

bool vis[N];
void dfs(int u, bool& ok, vector<int>& sz) {
    vis[u] = true;
    ok |= (sz[u] > 1);
    for(int v : T[u]) {
        if(vis[v]) continue;
        dfs(v, ok, sz);
    }
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
    for(int i = 1; i <= n; ++i) if(!dfn[i]) tarjan(i, -1);

    vector<int> sz(N, 0);
    for(int i = 1; i <= n; ++i) sz[id[i]]++;

    for(int i = 1; i <= n; ++i) {
        int u = id[i];
        for(int& j : G[i]) {
            int v = id[j];
            T[u].push_back(v);
        }
    }

    int ans = 0;
    for(int i = 1; i <= bcc; ++i) {
        if(vis[i]) continue;
        bool ok = false;
        dfs(i, ok, sz);
        ans += !ok;
    }
    printf("%d\n", ans);
    return 0;
}
```