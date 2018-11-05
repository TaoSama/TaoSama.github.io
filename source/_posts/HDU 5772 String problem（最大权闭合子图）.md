---
title: HDU 5772 String problem（最大权闭合子图）
categories:
  - 图论
  - 网络流
  - 
tags:
  - 最大权闭合子图
  - 最小割
  - 
date: 2016-08-05 19:21:10
toc: 
---

题意：
>$懒得翻译题目了 - -$

<!-- more -->
分析：
>$最大权闭合子图详细见胡波涛的论文$
$建图就源向正权点连边，容量是正权，负权就负权点向汇连边，容量是负权的绝对值$
$对于原图中依赖关系，保留在网络流图中，容量是INF$
$答案是\sum 正权-最小割$

---
>本题，官方题解写的很清楚了
![](http://7xru22.com1.z0.glb.clouddn.com/16-8-5/48099702.jpg)

代码:
```cpp
//
//  Created by TaoSama on 2016-08-02
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
const int M = 1e6 + 10;

//必须添加超源超汇 0->s n+1->t
//特判起终点相同的情况 -> WA
struct Edge {
    int v, nxt, cap, flow;
} edge[M];
int head[N], cnt;

void addEdge(int u, int v, int c1) {
    edge[cnt] = {v, head[u], c1, 0};
    head[u] = cnt++;
    edge[cnt] = {u, head[v], 0, 0};
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
            if(edge[i].cap > edge[i].flow && !lev[v]) {
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
        if(edge[i].cap > edge[i].flow && lev[v] == lev[u] + 1) {
            int d = dfs(v, t, min(delta, edge[i].cap - edge[i].flow));
            cur[u] = i;
            ret += d; delta -= d;
            edge[i].flow += d;
            edge[i ^ 1].flow -= d;

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

const int C = 100;
int n, a[C], b[C];
char str[C];

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int T; scanf("%d", &T);
    while(T--) {
        scanf("%d%s", &n, str + 1);
        for(int i = 0; i <= 9; ++i) scanf("%d%d", a + i, b + i);

        cnt = 0; memset(head, -1, sizeof head);
        int s = 0, t = n * n + n + 10 + 1;

        //w_ij: 1~n^2    s_i: n^2+1 ~ n^2+n
        //10: n^2+n+1 ~ n^2+n+10

        int sum = 0;
        int delta[] = {0, n * n, n* n + n};
        for(int i = 0; i < 10; ++i)
            addEdge(delta[2] + i + 1, t, -(a[i] - b[i])); //10 -> t
        for(int i = 1; i <= n; ++i) {
            addEdge(delta[1] + i, t, a[str[i] - '0']); //s_i -> t
            addEdge(delta[1] + i, delta[2] + str[i] - '0' + 1, INF); //s_i -> 10
            for(int j = 1; j <= n; ++j) {
                int w; scanf("%d", &w);
                sum += w;
                addEdge(s, (i - 1) * n + j, w); //s -> w_ij
                if(i == j) continue;
                addEdge((i - 1) * n + j, delta[1] + i, INF); //w_ij -> s_i
                addEdge((i - 1) * n + j, delta[1] + j, INF); //w_ij -> s_j
            }
        }

        static int kase = 0;
        printf("Case #%d: %d\n", ++kase, sum - dinic(s, t));
    }

    return 0;
}
```