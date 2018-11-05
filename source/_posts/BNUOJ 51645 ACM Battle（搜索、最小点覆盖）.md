---
title: BNUOJ 51645 ACM Battle（搜索、最小点覆盖）
categories:
  - 暴力
  - 搜索
  - dfs/bfs
  - 
tags:
  - 最小点覆盖
  - 
date: 2016-05-09 01:20:10
toc: 
---
题意：
>$1 \leq N \leq 1000,\ 1 \leq M \leq 2000，N个点M条边的无向图$
$求这个图的最小点覆盖集的大小，如果大于10输出GG$
<!-- more -->

分析：
>$由于最小点覆盖集的大小不超过10，所以可以直接裸搜10层$
>$裸搜的话，每条边分别尝试它的2个端点，维护一下覆盖的边的状态就好了$
$当然暴力选边以及更新覆盖边的状态是不兹磁的$
$我们可以维护一下，每个点盖了哪些边，这个用bitset来O(nm)预处理就好了$
$然后就可以搜辣，不能每次遍历所有的边，这样复杂度带上bitest就O(2^{10}\times \frac{m^2}{64})了$
$实际上由于选择都是一样的，我们可以用cur优化，每次选择第一个未被盖的边就可以$
$然后加上最优性剪枝，就跑得飞快辣$
$时间复杂度是O(nm+2^{10}\times \frac{m}{64})$

代码：
```cpp
//
//  Created by TaoSama on 2016-05-08
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
const int N = 2e3 + 10, INF = 0x3f3f3f3f, MOD = 1e9 + 7;

int n, m;
struct Edge {
    int u, v;
} edge[N];

bitset<2000> sta[1000];

void dfs(int dep, int& ans, bitset<2000> b) {
    if(dep >= ans || dep > 10) return;
    if(b.count() == m) {ans = dep; return;}
    for(int i = 0; i < m; ++i) {
        if(b[i] == 1) continue;
        int u = edge[i].u, v = edge[i].v;
        dfs(dep + 1, ans, b | sta[u]);
        dfs(dep + 1, ans, b | sta[v]);
        break;
    }
}

int main() {
#ifdef LOCAL
    freopen("C:\\Users\\TaoSama\\Desktop\\in.txt", "r", stdin);
//  freopen("C:\\Users\\TaoSama\\Desktop\\out.txt","w",stdout);
#endif
    ios_base::sync_with_stdio(0);
    clock_t _ = clock();

    int t; scanf("%d", &t);
    while(t--) {
        scanf("%d%d", &n, &m);
        for(int i = 0; i < m; ++i) {
            int u, v; scanf("%d%d", &u, &v);
            edge[i] = (Edge) {u, v};
        }
        for(int i = 0; i < n; ++i) {
            sta[i].reset();
            for(int j = 0; j < m; ++j)
                if(edge[j].u == i || edge[j].v == i)
                    sta[i][j] = 1;
        }
        int ans = INF;
        dfs(0, ans, 0);
        if(ans == INF) puts("GG");
        else printf("%d\n", ans);
    }

#ifdef LOCAL
    printf("\nTime cost: %.2fs\n", 1.0 * (clock() - _) / CLOCKS_PER_SEC);
#endif
    return 0;
}
```
