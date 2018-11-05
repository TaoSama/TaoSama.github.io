---
title: HDU 4635 Strongly connected（scc缩点）
categories:
  - 图论
  - 连通图
  - 
tags:
  - scc
  - 
date: 2016-03-26 02:20:10
toc: 
---
题意：
>$N,\ M\le 10^5的简单有向图，无重边自环$
$问最多添加多少条边使得这个图不成为强联通图，如果已经是输出-1$

<!-- more -->

分析：
>$参考出题人题解啦$
$图肯定可以分成两个部X和Y，只有X\to Y的边没有T\to X的边这样图就不强联通$
$那么要使得边数尽可能的多，则X部肯定是一个完全图，Y部也是$
$同时X部中每个点到Y部的每个点都有一条边$
$假设X部有x个点，Y部有y个点，有x+y=N，同时边数E=xy+x(x-1)+y(y-1)$
$整理得E=N^2-N-xy，当x+y为定值时，二者越接近，xy越大$
$要使得边数最多，那么X部和Y部的点数的个数差距就要越大$
$所以首先对于给定的有向图scc缩点$
$对于缩点后的每个点，如果它的出度或者入度为0，那么它才有可能成为X部或者Y部$
$所以只要求缩点之后的出度或者入度为0的点中，包含节点数最少的那个点$
$令它为一个部，其它所有点加起来做另一个部，就可以得到最多边数的图了$
$ans = E-M$
$时间复杂度为O(N+M)$

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
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

vector<int> G[N];
int dfn[N], low[N], in[N], id[N], scc, dfsNum;
int stk[N], top;

void tarjan(int u) {
    dfn[u] = low[u] = ++dfsNum;
    stk[++top] = u;
    in[u] = true;
    for(int v : G[u]) {
        if(!dfn[v]) {
            tarjan(v);
            low[u] = min(low[u], low[v]);
        } else if(in[v]) low[u] = min(low[u], dfn[v]);
    }
    if(low[u] == dfn[u]) {
        ++scc;
        while(true) {
            int v = stk[top--];
            in[v] = false;
            id[v] = scc;
            if(v == u) break;
        }
    }
}

int n, m;

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        for(int i = 1; i <= n; ++i) G[i].clear();
        for(int i = 1; i <= m; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            G[u].push_back(v);
        }

        scc = dfsNum = 0;
        memset(dfn, 0, sizeof dfn);
        for(int i = 1; i <= n; ++i) if(!dfn[i]) tarjan(i);

        static int kase = 0;
        if(scc == 1) {printf("Case %d: -1\n", ++kase); continue;}

        vector<bool> in(N, 0), out(N, 0);
        for(int i = 1; i <= n; ++i) {
            int u = id[i];
            for(int j : G[i]) {
                int v = id[j];
                if(u == v) continue;
                out[u] = in[v] = true;
            }
        }
        vector<int> cnt(N, 0);
        for(int i = 1; i <= n; ++i) ++cnt[id[i]];

        int x = INF;
        for(int i = 1; i <= scc; ++i)
            if(!in[i] || !out[i]) x = min(x, cnt[i]);
        int y = n - x;

        long long ans = 1LL * n * n - n - 1LL * x * y - m;
        printf("Case %d: %I64d\n", ++kase, ans);
    }
    return 0;
}
```