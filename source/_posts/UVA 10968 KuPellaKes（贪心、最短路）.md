---
title: UVA 10968 KuPellaKes（贪心、最短路）
categories:
  - 图论
  - 最短路
  - 
tags:
  - 贪心
  - 最短路
date: 2016-03-28 21:30:10
toc: 
---
题意：
>$N\le 2000个点的图，无重边自环，现要删去一些边$
$使得所有的点都是正偶度，保证图最多有2个奇度点$
$输出满足要求的要删去的最少的边，不能输出Poor\ Koorosh$

<!-- more -->

分析：
>$首先奇度点只有0个或2个，因为total\ degree = 2|E|，是偶度，必有偶数个奇度点$
$首先check图是不是合法，有没有孤立节点，或者1度的奇度点(删了就变了0了)$
$如果0个奇度点，答案显然是0，不然就是2个奇度点的最短路$
$因为删去2个奇度点的一条边，如果路上经过偶度点，显然会删去2条边(1入1出)$
$最终肯定是个合法的图，最短路显然是最优答案咯$
$但是不能经过2度的偶度点(去掉2度就为0了)$
$时间复杂度为O(n+m)$


代码：
```cpp
//
//  Created by TaoSama on 2016-03-27
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
#include <cassert>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
vector<int> G[N];

int d[N];
int bfs(int s, int t) {
    memset(d, -1, sizeof d);
    queue<int> q; q.push(s);
    d[s] = 0;
    while(q.size()) {
        int u = q.front(); q.pop();
        for(int i = 0; i < G[u].size(); ++i) {
            int v = G[u][i];
            if(G[v].size() == 2) continue;
            if(d[v] == -1) {
                d[v] = d[u] + 1;
                q.push(v);
            }
        }
    }
    return d[t];
}

int solve() {
    for(int i = 1; i <= n; ++i) G[i].clear();

    for(int i = 1; i <= m; ++i) {
        int u, v; scanf("%d%d", &u, &v);
        G[u].push_back(v);
        G[v].push_back(u);
    }

    int s, t; s = t = -1;
    for(int i = 1; i <= n; ++i) {
        if(G[i].size() <= 1) return -1;
        if(G[i].size() & 1) {
            if(s == -1) s = i;
            else t = i;
        }
    }

    //deg = 2E, so vertices of odd degree = 2
    if(s == -1) return 0;
    assert(t != -1);
    return bfs(s, t);
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    while(scanf("%d%d", &n, &m) == 2 && (n || m)) {
        int ans = solve();
        if(~ans) printf("%d\n", ans);
        else puts("Poor Koorosh");
    }
    return 0;
}
```