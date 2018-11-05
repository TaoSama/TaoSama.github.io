---
title: HDU 5639 Deletion（最大流）
categories:
  - 图论
  - 网络流
  - 
tags:
  - 拓扑排序
  - 
date: 2016-04-07 17:03:10
toc: 
---
题意：
>$N，M\le 2\times 10^3，N个点M条边的无向图$
$每次选择从图中删掉一些边，要求选出来的边构成的子图的每个连通块最多只有一个环$
$问最少需要删几次才能把所有边都删掉$

<!-- more -->

分析：
>![](http://7xru22.com1.z0.glb.clouddn.com/16-4-7/42023196.jpg)
$这想法真是炫酷，虽然我做过混合图欧拉回路那个题，早都忘了$
$第二种解法也是炫酷$

代码：
```cpp
//
//  Created by TaoSama on 2016-04-07
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
const int N = 4e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;
const int M = 8e3 + 10;

struct Edge {
    int v, nxt, cap;
} edge[M << 1];
int head[N], cnt;

void addDouble(int u, int v, int c1, int c2 = 0) {
    edge[cnt] = {v, head[u], c1};
    head[u] = cnt++;
    edge[cnt] = {u, head[v], c2};
    head[v] = cnt++;
}

int lev[N], cur[N];
bool bfs(int s, int t) {
    queue<int> q;
    memset(lev, 0, sizeof lev);
    q.push(s);  lev[s] = 1;
    while(q.size() && !lev[t]) {
        int u = q.front(); q.pop();
        for(int i = head[u]; ~i; i = edge[i].nxt) {
            int v = edge[i].v;
            if(edge[i].cap > 0 && !lev[v]) {
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
    for(int i = cur[u]; ~i; i = edge[i].nxt) {
        int v = edge[i].v;
        if(edge[i].cap > 0 && lev[v] == lev[u] + 1) {
            int d = dfs(v, t, min(delta, edge[i].cap));
            cur[u] = i;
            ret += d; delta -= d;
            edge[i].cap -= d;
            edge[i ^ 1].cap += d;

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

int n, m;
int u[M], v[M];

bool check(int x) {
    cnt = 0; memset(head, -1, sizeof head);
    int s = 0, t = n + m + 1;
    for(int i = 1; i <= n; ++i) addDouble(s, i, x);
    for(int i = 1; i <= m; ++i) {
        addDouble(u[i], i + n, 1);
        addDouble(v[i], i + n, 1);
        addDouble(i + n, t, 1);
    }
    return dinic(s, t) == m;
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        for(int i = 1; i <= m; ++i)
            scanf("%d%d", u + i, v + i);

        int l = 0, r = m;
        while(l <= r) {
            int m = l + r >> 1;
            if(check(m)) r = m - 1;
            else l = m + 1;
        }
        printf("%d\n", l);
    }
    return 0;
}
```