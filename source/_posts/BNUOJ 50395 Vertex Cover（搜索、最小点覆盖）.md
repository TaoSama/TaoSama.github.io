---
title: BNUOJ 50395 Vertex Cover（搜索、最小点覆盖）
categories:
  - 暴力
  - 搜索
  - dfs/bfs
  - 
tags:
  - 最小点覆盖
  - 
date: 2016-05-09 01:18:10
toc: 
---
题意：
>$2 \leq N \leq 500,\ 1 \leq M \leq \frac{n(n - 1)}{2}，N个点M条边的无向图$
$对于每条边(u,\ v)总有min(u,\ v)\le30，求这个图的最小点覆盖集的大小$
<!-- more -->

分析：
>$裸搜的话，每条边分别尝试它的2个端点，具体搜到多少完全不知道，复杂度是爆炸的$
$但是这个题有一个很好的性质，min(u,\ v)\le30，这样最小点覆盖集不会超过30$
$所以直接尝试去搜这30个点，尝试选或者不选（不选的话那么它连的所有点就都要选了）$
$不然这条边就没有点来覆盖了，这里要预处理一下这30个点所连的边$
$用bitset来预处理就好了，其实就是类似邻接表存一下图$
$搜的过程用bitset来保存选取的点覆盖集当作状态$
$看起来复杂度是2^{30}，加上最优性剪枝，以及bitset优化，实际上跑得飞快$
$时间复杂度O(跑得过)$

代码：
```cpp
//
//  Created by TaoSama on 2016-05-09
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
#include <bitset>

using namespace std;
#define pr(x) cout << #x << " = " << x << "  "
#define prln(x) cout << #x << " = " << x << endl
const int N = 1e5 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
bitset<500> g[30];

void dfs(int u, int& ans, bitset<500> vs) {
    int cnt = vs.count();
    if(cnt >= ans) return;
    if(u == min(n, 30)) {ans = cnt; return;}
    if(vs[u]) dfs(u + 1, ans, vs);
    else {
        vs[u] = 1;
        dfs(u + 1, ans, vs); //涂
        vs[u] = 0;
        dfs(u + 1, ans, vs | g[u]); //不涂它，连的点就得涂
    }
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    while(scanf("%d%d", &n, &m) == 2) {
        for(int i = 0; i < 30; ++i) g[i].reset();
        for(int i = 1; i <= m; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            --u; --v;
            if(u > v) swap(u, v);
            g[u][v] = 1;
            if(v < u) g[v][u] = 1;
        }

        int ans = INF;
        dfs(0, ans, 0);
        printf("%d\n", ans);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```
